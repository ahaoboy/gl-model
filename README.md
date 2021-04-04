fork from
https://github.com/mikolalysenko/teapot
https://github.com/mikolalysenko/bunny

## demo

```
import { bunny } from 'gl-model'

/*
bunny: {
    positions: [number, number, number][];
    cells: [number, number, number][];
}
*/

draw(bunny.positions, bunny.cells)
```

## regl demo

```
import REGL from "regl";
import { mat4 } from "gl-matrix";
import { bunny } from "gl-model";
import createCamera from "regl-camera-ts";
export default (canvas: HTMLCanvasElement) => {
  const regl = REGL(canvas);
  const camera = createCamera(regl, {
    center: [0, 5, 0],
    theta: 0,
    distance: 30,
  });
  interface Uniforms {
    model: mat4;
  }
  interface Attributes {
    position: REGL.Vec3[];
  }
  const drawBunny = regl<Uniforms, Attributes>({
    vert: `
  precision mediump float;
  attribute vec3 position;
  uniform mat4 model, view, projection;
  void main() {
    gl_Position = projection * view * model * vec4(position, 1);
  }`,

    frag: `
  precision mediump float;
  void main() {
    gl_FragColor = vec4(1, 1, 1, 1);
  }`,
    // this converts the vertices of the mesh into the position attribute
    attributes: {
      position: bunny.positions,
    },
    // and this converts the faces fo the mesh into elements
    elements: bunny.cells,
    uniforms: {
      model: mat4.identity(new Float32Array(16)),
    },
  });
  regl.frame(() => {
    camera((state) => {
      if (!state.dirty) return;
      regl.clear({
        depth: 1,
        color: [0, 0, 0, 1],
      });
      drawBunny();
    });
  });
};

```
