#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#include <emscripten.h>

#define CANVAS_WIDTH 500
#define CANVAS_HEIGHT 500

// คลาสเป้าหมาย
struct Target {
  int x;
  int y;
  int radius;
};

// ฟังก์ชันเริ่มต้นเกม
void startGame() {
  // สร้างเป้าหมาย
  Target target = {
    .x = CANVAS_WIDTH / 2,
    .y = CANVAS_HEIGHT / 2,
    .radius = 100,
  };

  // เริ่มต้นการยิงกระสุน
  setInterval(fire, 100);
}

// ฟังก์ชันวาดกราฟิกของเกม
void draw() {
  // วาดพื้นหลังสีขาว
  emscripten_set_canvas_element_size(canvas, CANVAS_WIDTH, CANVAS_HEIGHT);
  emscripten_set_clear_color(0, 0, 0, 255);
  emscripten_canvas_clear(canvas);

  // วาดเป้าหมาย
  drawTarget(target);

  // วาดกระสุน
  for (int i = 0; i < bullets.length; i++) {
    drawBullet(bullets[i]);
  }
}

// ฟังก์ชันยิงกระสุน
void fire() {
  // สร้างกระสุนใหม่
  Bullet bullet = {
    .x = mouseX,
    .y = mouseY,
    .vx = 100,
    .vy = 100,
  };

  // เพิ่มกระสุนลงในรายการกระสุน
  bullets.push(bullet);
}

// ฟังก์ชันตรวจจับการชนกันระหว่างกระสุนและเป้าหมาย
bool isCollidedWith(Bullet bullet, Target target) {
  return (bullet.x >= target.x - target.radius &&
          bullet.x <= target.x + target.radius &&
          bullet.y >= target.y - target.radius &&
          bullet.y <= target.y + target.radius);
}

// ฟังก์ชันอัปเดตคะแนน
void updateScore() {
  // เพิ่มคะแนน
  score++;

  // อัปเดตคะแนนบนหน้าจอ
  document.getElementById("score").innerHTML = score;
}

// ฟังก์ชันวาดเป้าหมาย
void drawTarget(Target target) {
  emscripten_set_color(255, 0, 0, 255);
  emscripten_arc(canvas, target.x, target.y, target.radius, 0, 2 * M_PI);
}

// ฟังก์ชันวาดกระสุน
void drawBullet(Bullet bullet) {
  emscripten_set_color(0, 255, 0, 255);
  emscripten_line(canvas, bullet.x,
