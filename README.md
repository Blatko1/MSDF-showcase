# *MSDF text rendering* - showcase

> ⚠️ This showcase is still being developed so you may not find some of the listed *features*. ⚠️

***MSDF-showcase*** is a showcase of *glsl shaders* utilizing **MSDF (multi-channel signed distance fields)** to render text in high quality while being *magnified*, *minified* or *rotated* in 3D space. It shows the basic usage of MSDF in text rendering with some additional, more advanced text modifiers.

All assets and characters are generated by the [msdfgen](https://github.com/Chlumsky/msdfgen) utility. Options to create the character assets were MTSDF (multi-channel *true* signed distance field) type with distance field pixel range of 6.

For *2D rendering*, you are better off using a rasterizer since there are no constant text size or rotation modifications, unlike in 3D rendering.

## Shader output in *real-time*

To see how the shaders work in real-time, you can download the [**SHADERed**](https://github.com/dfranx/SHADERed/releases) desktop application, open the ***'.sprj'*** file from the repo with the *SHADERed app* and see the output on the canvas. Learn more about *SHADERed* on their official [Tutorial website](https://shadered.org/docs/tutorials.html) or by watching [Youtube tutorials](https://www.youtube.com/playlist?list=PLK0EO-cKorzRAEfwHoJFiIldiyiyDR3-2).

## Basic MSDF usage

The next snippet shows the basic usage of a MSDF texture (*distance field pixel range 4*) to render text while preserving highest quality. For more info check out this [explanation](https://github.com/Chlumsky/msdfgen#using-a-multi-channel-distance-field) from the msdfgen creator.

```glsl
uniform sampler2D tex;

in vec2 uvCoord;
out vec4 outColor;

float median(float r, float g, float b) {
    return max(min(r, g), min(max(r, g), b));
}

float screenPxRange() {
    vec2 unitRange = vec2(4.0)/vec2(textureSize(tex, 0));
    vec2 screenTexSize = vec2(1.0)/fwidth(uvCoord);
    return max(0.5*dot(unitRange, screenTexSize), 1.0);
}

void main() {
    vec4 texel = texture(tex, uvCoord);
    float dist = median(texel.r, texel.g, texel.b);

    float pxDist = screenPxRange() * (dist - 0.5);
    float opacity = clamp(pxDist + 0.5, 0.0, 1.0);

    outColor = vec4(0.5, 0.5, 0.5, opacity);
}
```

## Features

In the shaders, you can find examples of:

- basic text rendering in 3D space with quality preservation while being *magnified*, *minified* or *rotated*,
- modifying text thickness,
- adding a colored outline with custom thickness to the text,
- applying softness to the body or the text outline,
- custom colored text drop shadow, 
- gamma correction

### Repo creation motive

There are two reasons why I wanted to make this repo. The first reason is that I am very interested in distance fields and their usage in rendering. Therefore, this repo helps me to understand and learn more. The second reason is the very rare findings on this topic on the internet. While searching for code examples and usage information regarding the subject, I could only find a few articles. Also, searching on GitHub, I often found the same basic *code snippet* repeating itself in almost every repo. Accordingly, I want to fill this repo with as much information and code examples (basic to advanced) as possible.

## Sources, links, articles and more info

Here you can find links to all the sources that helped me learn about MSDF and create the showcase.

> ⚠️ Constantly adding more! ⚠️

- [Text Rendering For Games](https://docs.google.com/presentation/d/1NCYNyR726F6j7vxwxFw0w0t8c6DUbiEMaxwMBbdP__0/edit#slide=id.g43674374e_046) - Presentation which covers three different ways to render text for games (one of them being SDFs).
- [The Valve paper](https://steamcdn-a.akamaihd.net/apps/valve/2007/SIGGRAPH2007_AlphaTestedMagnification.pdf) - The idea of using distance fields to render text was originally proposed in this short *pdf*.
- [Glyphs, shapes, fonts, signed distance fields. - YouTube](https://www.youtube.com/watch?v=1b5hIMqz_wM) - Short video explaining how to generate and use SDFs for rendering text or other 2D shapes.
- [Drawing Text with Signed Distance Fields in Mapbox GL](https://blog.mapbox.com/drawing-text-with-signed-distance-fields-in-mapbox-gl-b0933af6f817) - Short article that covers the idea behind SDFs and the basic usage of SDFs for text rendering.
- [Chlumsky/msdfgen: Multi-channel signed distance field generator](https://github.com/Chlumsky/msdfgen) - 
- [Implementing SDF/MSDF Font In OpenGL](https://medium.com/@calebfaith/implementing-msdf-font-in-opengl-ea09a9ab7e00) - 
- [MSDF text rendering performance demonstration - YouTube](https://www.youtube.com/watch?v=r-2z-ccuZKE) - Short video presenting the performance of using MSDFs when rendering a lot of text. Note that minified text looks much worse then the rest but I am convinced that methods used in this showcase avoid that efficiently.
