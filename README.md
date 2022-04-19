frameRate(60);

var screen = "mainMenu";
var grassX = 0;
var gravity = 2;
var gameSpeed = 3;
var ground = 348;
var gameOver = false;
var score = 0;

var pixels = [
    [0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 1, 1, 3, 3, 3, 1, 2, 2, 1, 0, 0, 0, 0],
    [0, 0, 0, 1, 3, 3, 3, 3, 1, 2, 2, 2, 2, 1, 0, 0, 0],
    [0, 1, 1, 1, 1, 3, 3, 3, 1, 2, 2, 2, 1, 2, 1, 0, 0],
    [1, 4, 4, 4, 4, 1, 3, 3, 1, 2, 2, 2, 1, 2, 1, 0, 0],
    [1, 4, 4, 4, 4, 4, 1, 3, 3, 1, 2, 2, 2, 2, 1, 0, 0],
    [1, 4, 4, 4, 4, 4, 1, 3, 3, 3, 1, 1, 1, 1, 1, 1, 0],
    [0, 1, 4, 4, 4, 1, 3, 3, 3, 1, 5, 5, 5, 5, 5, 5, 1],
    [0, 0, 1, 1, 1, 3, 3, 3, 1, 5, 1, 1, 1, 1, 1, 1, 0],
    [0, 0, 1, 3, 3, 3, 3, 3, 3, 1, 5, 5, 5, 5, 5, 1, 0],
    [0, 0, 0, 1, 1, 3, 3, 3, 3, 3, 1, 1, 1, 1, 1, 0, 0],
    [0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0],
];

var createFlappyBirdImage = function(x, y, bs) {
    
    background(0, 0, 0, 0);

    noStroke();

    for (var a = 0; a < pixels[0].length; a++) {
        for (var b = 0; b < pixels.length; b++) {

            switch (pixels[b][a]) {

                case 1:
                    fill(0);
                    rect(a * bs + x, b * bs + y, bs, bs);
                    break;
                case 2:
                    fill(255);
                    rect(a * bs + x, b * bs + y, bs, bs);
                    break;
                case 3:
                    fill(255, 255, 0);
                    rect(a * bs + x, b * bs + y, bs, bs);
                    break;
                case 4:
                    fill(255, 245, 0);
                    rect(a * bs + x, b * bs + y, bs, bs);
                    break;
                case 5:
                    fill(255, 65, 0);
                    rect(a * bs + x, b * bs + y, bs, bs);
                    break;
            }
        }
    }
    
    return get(0, 0, pixels[0].length * bs, pixels.length * bs);
};

var flappyBirdImage = createFlappyBirdImage(0, 0, 3);

var FlappyBird = function(x, y) {

    this.x = x;
    this.y = y;
    this.w = 50;
    this.h = 35;
    this.angle = 0;
    this.jumpSpeed = 7.5;
    this.fallSpeed = 0.65;
    this.bounceSpeed = 2.5;
};

var flappyBird = new FlappyBird(50, 150);

FlappyBird.prototype.draw = function() {
    
    pushMatrix();
    translate(this.x, this.y);
    rotate(this.angle);
    image(flappyBirdImage, 0, 0);
    popMatrix();
};

FlappyBird.prototype.fly = function() {

    if (mouseIsPressed) {
        
        if (this.angle > -45) {
            this.angle -= this.bounceSpeed;
        }
    
        this.y -= this.jumpSpeed;
    }
};

FlappyBird.prototype.fall = function() {

    if (this.angle < 90) {
        this.angle += this.fallSpeed;
    }

    this.y += gravity;
};

FlappyBird.prototype.die = function() {

    if (this.y < 0 || this.y + this.h > ground) {
        gameOver = true;
    }
};

var Pipe = function(x, y, h1, h2) {

    this.x = x;
    this.y = y;
    this.w = 50;
    this.h1 = h1;
    this.h2 = h2;
};

var pipes = [
    new Pipe(400, 0, random(25, 125), random(25, 125)),
    new Pipe(700, 0, random(25, 125), random(25, 125))
];

Pipe.prototype.draw = function() {

    strokeWeight(2);
    stroke(0);
    fill(0, 125, 0);

    rect(this.x, this.y, this.w, this.h1);
    rect(this.x - 3, this.y + this.h1 - 20, this.w + 6, 20);
    rect(this.x, ground - this.h2, this.w, this.h2);
    rect(this.x - 3, ground - this.h2, this.w + 6, 20);
};

Pipe.prototype.move = function() {

    this.x -= gameSpeed;

    if (this.x + this.w < 0) {
        score++;
        this.x = 600;
        this.h1 = random(25, 125);
        this.h2 = random(25, 125);
    }
};

Pipe.prototype.collision = function() {
    
    if ((flappyBird.x + flappyBird.w > this.x && flappyBird.x < this.x + this.w) && ((flappyBird.y < this.h1) || (flappyBird.y + flappyBird.h > ground - this.h2))) {
        gameOver = true;
    }
};

var gameBackground = function() {

    background(85, 160, 200);

    noStroke();
    fill(255);
    rect(0, 255, 400, 75);

    for (var i = 25; i < 475; i += 125) {
        arc(i, 255, 75, 50, 180, 360);
        arc(i + 65, 265, 75, 50, 180, 360);
    }

    strokeWeight(3);
    stroke(75, 130, 180);
    fill(175, 190, 225);

    for (var i = 0; i < 400; i += 150) {
        rect(i, 260, 25, 50);
        rect(i - 10, 275, 25, 55);
        rect(i + 85, 265, 30, 55);
        rect(i + 95, 285, 30, 55);
        rect(i + 20, 290, 50, 25);
        
        beginShape();
        vertex(i + 225 - 175, 210 + 50);
        vertex(i + 240 - 175, 210 + 50);
        vertex(i + 240 - 175, 200 + 52);
        vertex(i + 255 - 175, 200 + 52);
        vertex(i + 255 - 175, 270 + 50);
        vertex(i + 225 - 175, 260 + 50);
        endShape(CLOSE);
    }

    noStroke();
    fill(80, 175, 80);
    rect(0, 310, 400, 50);
    
    strokeWeight(3);
    stroke(45, 125, 45);

    for (var i = 50; i < 475; i += 100) {
        arc(i, 350 - 35, 65, 25, 180, 360);
        arc(i - 40, 355 - 35, 65, 25, 180, 360);
        arc(i, 365 - 35, 65, 25, 180, 360);
    }

    noStroke();
    fill(175, 165, 85);
    rect(0, 365, 400, 35);
    fill(90, 155, 50);
    rect(0, 350, 400, 12);
    fill(145, 135, 50);
    rect(0, 362, 400, 4);
    fill(65, 135, 35);

    for (var x = 0; x <= 400; x += 25) {
        rect(grassX + x + 10, 350, 12, 4);
        rect(grassX + x + 5, 354, 12, 4);
        rect(grassX + x, 358, 12, 4);
    }

    grassX -= gameSpeed;

    if (grassX < -25) {
        grassX = 0;
    }
    
    strokeWeight(3);
    stroke(0);
    line(0, ground, 400, ground);
};

var button = function(x, y, w, h, label, nextScreen) {

    stroke(0);
    strokeWeight(3);
    
    fill(255);
    rect(x, y, w, h);

    if (mouseX > x && mouseX < x + w && mouseY > y && mouseY < y + h) {
        fill(245, 0, 0);
        cursor(HAND);

        if (mouseIsPressed) {
            screen = nextScreen;
        }

    } else {
        fill(255, 0, 0);
    }

    noStroke();
    rect(x + 5, y + 5, w - 10, h - 10);

    fill(255);
    textSize(20);
    text(label, x + (w / 2), y + (h / 2));
};

var mainMenu = function() {

    gameBackground();

    textSize(35);
    fill(65, 135, 35);
    text("Flappy Bird", 155, sin(frameCount * 10) * 5 + 100);
    
    image(flappyBirdImage, 275, sin(frameCount * 10) * 5 + 85);

    button(137.5, 250, 125, 40, "Start", "play");
    
    textSize(20);
    fill(255);
    text("Kevin 23", 200, 382.5);
};

var gameOverScreen = function() {

    noLoop();

    textSize(50);
    fill(215, 130, 20);
    text("Game Over", 200, 55);

    strokeWeight(3);
    stroke(0);
    fill(175, 165, 85);
    rect(100, 115, 200, 120);
    strokeWeight(3);
    stroke(175, 125, 25);
    rect(110, 125, 180, 100);
    fill(175, 125, 25);
    textSize(25);
    text("Score", 200, 150);
    textSize(35);
    fill(255);
    text(score, 200, 195);
};

var play = function() {

    gameBackground();

    flappyBird.draw();
    flappyBird.fly();
    flappyBird.fall();
    flappyBird.die();
    
    for (var i = 0; i < pipes.length; i++) {
        pipes[i].draw();
        pipes[i].move();
        pipes[i].collision();
    }

    if (!gameOver) {
        fill(255);
        textSize(35);
        text(score, 200, 35);
    } else {
        gameOverScreen();
    }
};

var game = function() {

    textFont(createFont("sans-serif"));
    textAlign(CENTER, CENTER);
    cursor(ARROW);

    switch (screen) {
        
        case "mainMenu":
            mainMenu();
            break;
        case "play":
            play();
            break;
    }
};

draw = function() {

    game();
};
