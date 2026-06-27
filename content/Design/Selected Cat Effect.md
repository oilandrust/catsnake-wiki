**Try**
- Dimm albedo when not selected
- Toon Shading + Colored Rim Light, without specular when selected

https://roystan.net/articles/outline-shader/

https://www.shadertoy.com/view/sfj3WR

[

# Becoming a Unity VFX Artist -07- Fresnel

![](https://www.gstatic.com/images/branding/product/1x/youtube_32dp.png)YouTube·Brandon Garman·13 Apr 2023



](https://www.youtube.com/watch?v=pYVjP7_xKUk)


**Bloom**

- **Cost:** medium to high.
- **Why:** full-screen post-processing, often multiple blur passes.
- **Scales with:** screen resolution, not number of glowing objects.
- **Good for:** a few nice soft glows, magical objects, polished look.
- **Bad for:** low-end mobile/Switch if overused or high resolution.
- **Best CatSnake use:** very subtle global bloom.

**Fresnel rim glow**

- **Cost:** low.
- **Why:** usually just a bit of extra shader math on the object material.
- **Scales with:** number/size of glowing objects on screen.
- **Good for:** characters, pickups, interactable objects.
- **Bad for:** creating actual soft halos around objects.
- **Best CatSnake use:** highlight the cat/object edges cheaply.

**Outline shader**

- **Cost:** low to medium, depending on method.
- **Why:** common “inverted hull” outline renders the mesh a second time; screen-space outlines need depth/normal passes and post-processing.
- **Scales with:**
    - inverted hull: number of outlined objects / mesh complexity
    - screen-space: screen resolution
- **Good for:** readable silhouettes, selected objects, puzzle clarity.
- **Bad for:** subtle natural glow; can look cartoony/gamey.
- **Best CatSnake use:** highlight interactable blocks or the player character.
