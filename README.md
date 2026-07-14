# TV Static CAPTCHA System (POC/Demo)

⚠️ **This is a Proof of Concept / Demo** - Not a complete production system.

A cutting-edge CAPTCHA verification system using kinetic TV static animation that resists AI and bot bypass attempts.

## Features

- **Kinetic Static Animation**: Dynamic TV static background with moving text elements at different speeds
- **AI-Resistant Design**: The moving static pattern makes it extremely difficult for computer vision to extract characters
- **Bot-Proof**: Real-time animation rendering prevents static analysis and OCR attacks
- **Dark Theme UI**: Modern, sleek interface with responsive design
- **Zero Dependencies**: Pure vanilla JavaScript, no external libraries required
- **Lightweight**: Single HTML file, easy to integrate and deploy

## How It Works

The system generates random 4-character codes displayed within animated TV static:
- **Background Static**: Moves upward at 1.5px/frame
- **Text Layer**: Moves downward at 2.25px/frame, creating visual separation
- **Noise Grid**: Continuously regenerated high-density noise pattern
- **Canvas Rendering**: Real-time pixel-by-pixel animation using Canvas 2D API

This differential motion between static and text makes character extraction nearly impossible for automated systems.

## Character Set

Uses easily distinguishable characters to reduce human confusion:
- `B K H M R X W Y 3 4 6 8 9`

Excludes visually similar characters (0/O, 1/I/L, etc.)

## Usage

### Direct HTML
Simply open `index.html` in any modern web browser.

### Integration
Embed in your application:

```html
<iframe src="https://yourusername.github.io/tv-static-qr-captcha/" width="600" height="350"></iframe>
```

### Server-Side Verification
The client validates locally. For production, implement server-side verification:

```javascript
// Send captcha value to your backend
fetch('/verify-captcha', {
    method: 'POST',
    body: JSON.stringify({ captchaValue: userInput })
})
```

## Technical Details

- **Canvas Size**: 500x200 pixels
- **Pixel Scale**: 1:1 for fine detail
- **Animation FPS**: 60 (via requestAnimationFrame)
- **Noise Resolution**: 400 rows × simWidth columns
- **Font**: Arial Black, 84px, 900 weight

## Browser Support

Works in all modern browsers supporting:
- HTML5 Canvas
- Canvas 2D Context
- requestAnimationFrame
- ES6 JavaScript

## Why It's Bot-Resistant

1. **Real-time Rendering**: Each frame is dynamically generated
2. **Motion-Based Obscuration**: Characters move at different speeds than background
3. **High-Density Noise**: Pixel-level static makes pattern extraction fail
4. **No Static Images**: Nothing to analyze with traditional image processing
5. **Anti-Scraping**: Canvas rendering prevents direct DOM inspection

## Customization

Edit the following in `index.html`:

```javascript
const bgSpeed = 1.5;        // Background movement speed
const textSpeed = 2.25;     // Text movement speed
const noiseRows = 400;      // Noise grid height
const chars = "BKHMRXWY34689"; // Character set
```

## Demo

Visit: https://sheikhlipu123.github.io/tv-static-qr-captcha/

## ⚠️ Important - POC/Demo Disclaimer

This is a **proof of concept and demonstration only**. It is not a complete, production-ready CAPTCHA system. 

### Limitations:
- Client-side validation only (no backend verification)
- No session management
- No rate limiting
- No token/challenge generation
- No database integration
- No analytics or logging
- Not suitable for critical security applications without major modifications

### For Production Use:
1. Implement server-side verification
2. Add challenge/response tokens
3. Implement rate limiting and cooldown periods
4. Log failed attempts
5. Add HTTPS-only transmission
6. Consider combining with additional security layers
7. Test against advanced attack vectors

## License

MIT License - Feel free to use and modify for your projects.

## Security Notes

This CAPTCHA is designed to prevent basic automated attacks. However:
- **Always implement server-side verification** for production
- Use HTTPS in production environments
- Combine with rate limiting
- Consider implementing cooldown after multiple failed attempts
- Use as part of a defense-in-depth strategy, not as a standalone solution

## Contributing

Found a bypass? Have improvements? Feel free to submit issues and pull requests!

---

**Created by:** Sheikhlipu123  
**Status:** POC/Demo  
**Last Updated:** 2026-07-14
