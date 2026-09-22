# OPEN WINDOWS

## CLASSIC SETUP

```kdl
window-open {
    duration-ms 150
    curve "ease-out-expo"
}

window-close {
    duration-ms 150
    curve "ease-out-quad"
}
```

## SLIDE UP

```kdl
window-open {
    duration-ms 150
    curve "ease-out-cubic"

    custom-shader r"
        vec4 open_color(vec3 coords_geo, vec3 size_geo) {
            float p = niri_clamped_progress; // 0.0 -> 1.0
            vec2 uv = coords_geo.xy;

            // Subtract (1.0 - p) to pull the window in from the BOTTOM.
            vec2 sample_uv = vec2(uv.x, uv.y - (1.0 - p));

            // Map coordinates to texture space
            vec3 tc = niri_geo_to_tex * vec3(sample_uv, 1.0);
            vec4 win = texture2D(niri_tex, tc.st);

            // Clip anything outside the [0.0, 1.0] unit square
            float inside = step(0.0, sample_uv.y) * step(sample_uv.y, 1.0);

            return win * inside;
        }
    "
}
```

## SLIDE DOWN

```kdl
 window-open {
    duration-ms 150
    curve "ease-out-cubic"

    custom-shader r"
        vec4 open_color(vec3 coords_geo, vec3 size_geo) {
            float p = niri_clamped_progress; // 0.0 -> 1.0
            vec2 uv = coords_geo.xy;

            // In GLSL Y=1.0 is TOP and Y=0.0 is BOTTOM.
            // When p=0, offset is +1.0 (pushes sampling point up, pulling window from above).
            vec2 sample_uv = vec2(uv.x, uv.y + (1.0 - p));

            // Map coordinates to texture space
            vec3 tc = niri_geo_to_tex * vec3(sample_uv, 1.0);
            vec4 win = texture2D(niri_tex, tc.st);

            // Clip anything outside the [0.0, 1.0] unit square
            float inside = step(0.0, sample_uv.y) * step(sample_uv.y, 1.0);

            return win * inside;
        }
    "
}
```

# CLOSE WINDOWS

## SLIDE UP

```kdl
window-close {
    duration-ms 150
    curve "linear"

    custom-shader r"
        vec4 close_color(vec3 coords_geo, vec3 size_geo) {
            float p = niri_clamped_progress; // 0.0 (open) -> 1.0 (closed)
            vec2 uv = coords_geo.xy;

            // Adding p shifts texture sampling downward,
            // causing the rendered window to slide UPWARD.
            vec2 sample_uv = vec2(uv.x, uv.y + p);

            // Map coordinates to texture space
            vec3 tc = niri_geo_to_tex * vec3(sample_uv, 1.0);
            vec4 win = texture2D(niri_tex, tc.st);

            // Clip pixels outside the valid texture area
            float inside = step(0.0, sample_uv.y) * step(sample_uv.y, 1.0);

            return win * inside;
        }
    "
}
```

## SLIDE DOWN

```kdl
window-close {
    duration-ms 150
    curve "linear"

    custom-shader r"
        vec4 close_color(vec3 coords_geo, vec3 size_geo) {
            float p = niri_clamped_progress; // 0.0 (open) -> 1.0 (closed)
            vec2 uv = coords_geo.xy;

            // Subtracting p shifts the texture sampling upward, 
            // causing the rendered window to slide DOWNWARD.
            vec2 sample_uv = vec2(uv.x, uv.y - p);

            // Map coordinates to texture space
            vec3 tc = niri_geo_to_tex * vec3(sample_uv, 1.0);
            vec4 win = texture2D(niri_tex, tc.st);

            // Clip pixels outside the valid texture area
            float inside = step(0.0, sample_uv.y) * step(sample_uv.y, 1.0);

            return win * inside;
        }
    "
}
```
