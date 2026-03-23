# Assignment-7---Shaders
I originally planned to apply a distorted shader only to the 😱 emoji to make it feel more lively.
But after spending a lot of time on it, I still couldn’t separate that character out on its own for the shader.
So for now, the entire screen has a subtle shaking effect instead.
I guess if I want the meteor to have a subtle glow, that would also be done with a shader....?
https://editor.p5js.org/LucyZheng-5010/full/66gX1dqbR

```js
let agents = [];
let count = 100;
let words = ["☄️"];
let myShader;

//shader
let shaderSrc = `
precision lowp float;
uniform sampler2D tex0;
varying vec2 vTexCoord;
uniform float time;

void main() {
  vec2 uv = vTexCoord;
  float wave = sin(uv.y * 20.0 + time * 2.0) * 0.005;
  vec4 col = texture2D(tex0, uv + vec2(wave, 0.0));
  gl_FragColor = col;
}
`;

function setup() {
  createCanvas(600, 400);
  textAlign(CENTER, CENTER);
  myShader = createFilterShader(shaderSrc);

  for (let i = 0; i < count; i++) {
    agents.push({
      x: random(width),
      y: random(height),
      word: random(words),
      update() {
        let d = dist(mouseX, mouseY, this.x, this.y);
        if (d < 100) {
          let angle = atan2(this.y - mouseY, this.x - mouseX);
          let speed = map(d, 0, 100, 5, 0);
          this.x += cos(angle) * speed;
          this.y += sin(angle) * speed;
        }
      },
      draw() {
        let d = dist(mouseX, mouseY, this.x, this.y);
        if(d < 100) text("😱", this.x, this.y - 30);
        textSize(40);
        text(this.word, this.x, this.y);
      }
    });
  }
}

function draw() {
  background(0);
  agents.forEach(a => {
    a.update();
    a.draw();
  });
  
  textSize(80);
  text("🦖", mouseX, mouseY);

  myShader.setUniform('time', millis() / 1000.0);
  filter(myShader); 
