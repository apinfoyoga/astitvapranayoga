# Hero Image Guidelines

This document outlines the standard procedure for adding, processing, and maintaining the hero slider images on the Astitva Prana Yoga Shala website. This ensures load performance remains fast, image crops are consistent, and the animations stay perfectly synchronized.

## Image Dimensions & Processing
Original raw camera files (often 5000+ pixels wide and 3MB+ in size) are too heavy for smooth web loading. 

**Standard Rules:**
1. **Maximum Dimension:** Scale down any image so its largest dimension is `1920px`. The height or width will proportionally scale down.
2. **File Format:** Save images as `JPEG` with quality set to `85` to maintain details while significantly reducing file size.
3. **Color Profile:** Ensure images are saved in the `sRGB` color space (convert if necessary, e.g., from CMYK).
4. **Naming Convention:** Use `hero_1.jpg`, `hero_2.jpg`, etc., consecutively.

### Python Script Used for Scaling
If you add more images in the future, you can execute a script similar to this to process new ones from this directory:

```python
import os
from PIL import Image

folder = "./" # Path to assets/Hero
files = ["new_image.jpeg"] # Your new images

for f in files:
    path = os.path.join(folder, f)
    with Image.open(path) as img:
        if img.mode != "RGB":
            img = img.convert("RGB")
        
        max_dim = max(img.height, img.width)
        if max_dim > 1920:
            scale = 1920 / float(max_dim)
            new_width = int(img.width * scale)
            new_height = int(img.height * scale)
            img = img.resize((new_width, new_height), Image.Resampling.LANCZOS)
            
        out_path = os.path.join(folder, "hero_X.jpg")
        img.save(out_path, "JPEG", quality=85)
```

## CSS & HTML Setup
The hero slider is powered by a seamless CSS fade animation and Javascript dots. If you add or remove slides:

1. **HTML Updates (`index.html`):**
   - Add a new `<div class="hero-slide hero-slide-5" aria-hidden="true"></div>` block to the `.hero-slider` container.
   - Add an additional `<span class="dot"></span>` inside `.hero-dots`.

2. **CSS Background Scaling (`styles.css`):**
   - We use `background-size: cover;` which ensures the whole screen is filled responsibly.
   - We use `background-position: center 30%;`. This offsets the vertical center slightly towards the top to ensure practitioners' heads/torsos in vertical/solo pose images aren't cropped out on wide desktop monitors.

3. **Animation Timing (`styles.css`):**
   - Each slide is displayed for exactly **5 seconds**. 
   - Time allocation rule: `Total Duration = Number of Slides × 5s`.
   - Currently, there are 4 slides, so `animation: heroFade 20s infinite;`.
   - Update `animation-delay` offsets per slide sequentially (e.g., `0s`, `5s`, `10s`, `15s`).
   - The `@keyframes heroFade` percentage chunks dictates the crossfade. Divide `5s` fade-in, visibility, and fade-out evenly relative to the new `Total Duration` percentage. For a 20s loop, 5s equates to 25% increments.
