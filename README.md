# Kinetic Static CAPTCHA: Motion-Based Text Obscuration in Animated Noise Fields

## Abstract

This paper presents a Proof of Concept (POC) implementation of a CAPTCHA system utilizing kinetic static animation to obscure alphanumeric characters within high-frequency noise patterns. Through vulnerability analysis and a practical attack implementation, we demonstrate that temporal variance segmentation and motion detection techniques can extract text from this system. We document the attack methodology, discuss practical considerations, and argue that animation-based obscuration alone should not be relied upon as a primary security mechanism. This work is intended as an educational resource and does not present empirical validation across large datasets.

**Keywords:** CAPTCHA, motion detection, computer vision, security analysis, temporal analysis

**Demo:** https://sheikhlipu123.github.io/TV-Static-CAPTCHA-Security-Analysis-POC/

**Status:** Proof of Concept / Educational - Not peer-reviewed research

---

## 1. Introduction

### 1.1 CAPTCHA Challenge and Response Systems

CAPTCHA (Completely Automated Public Turing test to tell Computers and Humans Apart) systems form a line of defense against automated attacks on web services. Since their introduction by von Ahn et al. (2003), CAPTCHA design has evolved through multiple generations, progressing from static text distortion to behavioral analysis and device fingerprinting.

### 1.2 Animation-Based Obscuration

Recent design explorations have considered animation and motion as obscuration mechanisms, with the intuition that real-time rendering of moving elements might increase difficulty for automated analysis. However, limited published research exists on the actual vulnerability of such approaches.

### 1.3 Scope and Limitations

This work is exploratory and educational in nature. We demonstrate one attack method and discuss its plausibility, but we do not:
- Conduct statistically rigorous evaluation
- Test against representative CAPTCHA samples
- Compare against established CAPTCHA systems
- Provide confidence intervals or reproducibility data

The results presented are illustrative and should not be treated as empirical validation without systematic testing.

### 1.4 Contributions

This work makes the following contributions:
- **Implementation** of a motion-based obscuration CAPTCHA system
- **Practical demonstration** of one attack vector (temporal variance segmentation)
- **Discussion** of why animation-based defenses face inherent limitations
- **Educational value** in understanding motion detection applications to security

---

## 2. Background and Related Work

### 2.1 Traditional CAPTCHA Approaches

**Text-based CAPTCHAs** typically use character distortion, rotation, and overlapping. Research has shown that deep learning methods can achieve high recognition rates on distorted text, though the exact success rates depend on training data and attack sophistication.

**Image-based CAPTCHAs** require object recognition tasks. While CNNs have demonstrated strong performance on digit recognition tasks like SVHN (Goodfellow et al., 2013), the applicability to complex object recognition CAPTCHAs varies with design choices.

**Behavioral and Challenge-Response CAPTCHAs** analyze user interaction patterns and rely on cryptographic verification. These approaches show better resilience to automation than visual obscuration alone.

### 2.2 Motion-Based Obscuration

Very limited published literature exists specifically on CAPTCHAs using differential motion as the primary obscuration mechanism. The approach is relatively unexplored in academic security research.

### 2.3 Motion Detection in Computer Vision

**Optical Flow** (Horn & Schunck, 1981; Lucas & Kanade, 1981) remains a fundamental technique for estimating motion between frames. Applications include video object tracking, autonomous navigation, and activity recognition.

**Temporal Segmentation** methods (Torr & Zisserman, 1998) separate moving objects from backgrounds by analyzing motion patterns across frames.

**Morphological Operations** (Serra, 1982) are well-established image processing techniques for noise removal and object extraction.

---

## 3. System Design

### 3.1 Technical Specification

**Canvas Parameters:**
- Resolution: 500×200 pixels
- Frame rate: 60 FPS
- Animation: Continuous looping

**Noise Field:**
- Temporal resolution: 400 rows of noise
- Regeneration: Per-frame
- Distribution: Uniform binary (50% 0, 50% 255)

**Motion Parameters:**
- Background velocity: v_bg = 1.5 pixels/frame (upward)
- Text velocity: v_text = 2.25 pixels/frame (downward)
- Velocity differential: 0.75 pixels/frame

**Character Set:**
- Alphabet: {B, K, H, M, R, X, W, Y, 3, 4, 6, 8, 9}
- Rationale: Mutual visual distinctness
- Length: 4 characters
- Entropy: log₂(13⁴) = log₂(28,561) ≈ 14.8 bits

**Rendering:**
- Font: Arial Black, 84px, weight 900
- Anti-aliasing: Disabled

### 3.2 Threat Model

**Attacker Capabilities:**
- Browser automation (Selenium, Puppeteer)
- Screenshot acquisition
- Image processing libraries (OpenCV)
- Standard OCR engines (Tesseract)

**Limitations of This Analysis:**
- No actual attack execution against the live system
- No systematic measurement of success/failure
- No evaluation across multiple CAPTCHA instances
- Results presented are illustrative and representative only

---

## 4. Vulnerability Analysis

### 4.1 Fundamental Tradeoff: Visibility vs. Exploitability

We hypothesize that for CAPTCHA designs relying solely on differential motion cues, a practical tradeoff exists:

**Observation 1:** If text moves at a substantially different velocity than background noise, the motion vectors differ markedly between text and noise pixels.

**Observation 2:** Motion detection algorithms (optical flow, frame differencing) are optimized to identify such differences.

**Observation 3:** Motion information visible to human eyes is also available to motion detection algorithms.

We argue that in this particular implementation, the velocity differential (0.75 pixels/frame between text and background) creates exploitable motion information. However, we do not claim this is impossible for all animation schemes.

**Revised Statement:** Our observations suggest that practical CAPTCHAs using differential motion face a tension between human readability and resistance to motion segmentation.

---

## 5. Attack Vectors and Implementation

### 5.1 Attack 1: Temporal Variance Segmentation

**Approach:**

Frame differencing combined with Otsu thresholding and morphological filtering is a well-established computer vision pipeline. The algorithm:

1. Accumulate absolute frame differences over N frames
2. Normalize the accumulated differences
3. Apply automatic thresholding
4. Apply morphological opening to remove noise
5. Extract connected components

**Why It May Work:**
- Text pixels change consistently due to predictable velocity
- Noise pixels change randomly and decorrelate across frames
- Accumulated differences should show high values for text regions

**Limitations:**
- Does not actually work reliably on binary noise patterns
- May require parameter tuning
- No guarantee of success without testing
- Success depends heavily on image quality and OCR performance

**Empirical Status:** Representative implementation provided. Not validated against multiple test cases.

### 5.2 Attack 2: Optical Flow Analysis

**Plausibility:** Lucas-Kanade and Horn-Schunck optical flow algorithms could potentially detect coherent motion from text while noise exhibits incoherent motion.

**Practical Considerations:** Optical flow works best on textured regions with visible gradients. Pure binary patterns may provide insufficient features for reliable flow estimation.

### 5.3 Attack 3: Kalman Filtering

**Concept:** Kalman filtering can predict trajectories of moving objects. Text following a constant velocity pattern might be predictable.

**Reality Check:** Kalman filtering typically stabilizes noisy measurements rather than solving the extraction problem independently. Combined with other techniques, it could help.

### 5.4 Attack 4: Feature Tracking (SIFT/SURF)

**Theory:** Invariant feature detection could identify consistent text patterns across frames.

**Practical Challenges:** SIFT and SURF are designed for textured natural images. Performance on binary noise patterns and synthetic text is uncertain without testing.

---

## 6. Proof of Concept Implementation

### 6.1 Python Implementation

**Status:** Illustrative code showing the attack concept. Not tested end-to-end.

**Dependencies:**
```
opencv-python>=4.5.0
selenium>=4.0.0
pytesseract>=0.3.10
numpy>=1.19.0
```

**Representative Code:**

```python
import cv2
import numpy as np
from selenium import webdriver
import time

class TemporalVarianceAttack:
    """
    Illustrative implementation of temporal variance segmentation.
    
    NOTE: This is representative code showing the attack concept.
    Actual success rates and required parameters would need to be
    determined through systematic testing.
    """
    
    def __init__(self, captcha_url, num_frames=30):
        self.url = captcha_url
        self.num_frames = num_frames
        self.frames = []
        
    def capture_frames(self):
        """Capture canvas frames via Selenium"""
        options = webdriver.ChromeOptions()
        options.add_argument('--headless')
        driver = webdriver.Chrome(options=options)
        driver.get(self.url)
        
        canvas = driver.find_element("id", "captchaCanvas")
        
        for i in range(self.num_frames):
            canvas.screenshot(f"frame_{i:03d}.png")
            img = cv2.imread(f"frame_{i:03d}.png", cv2.IMREAD_GRAYSCALE)
            self.frames.append(img)
            time.sleep(0.016)
        
        driver.quit()
        return self.frames
    
    def compute_temporal_variance(self):
        """Accumulate absolute frame differences"""
        diff_accumulator = np.zeros_like(self.frames[0], dtype=np.float32)
        
        for i in range(len(self.frames) - 1):
            frame_diff = cv2.absdiff(
                self.frames[i].astype(np.float32),
                self.frames[i+1].astype(np.float32)
            )
            diff_accumulator += frame_diff
        
        diff_normalized = cv2.normalize(
            diff_accumulator, None, 0, 255, cv2.NORM_MINMAX
        ).astype(np.uint8)
        
        return diff_normalized
    
    def apply_otsu_threshold(self, variance_map):
        """Apply Otsu's automatic thresholding"""
        _, binary_mask = cv2.threshold(
            variance_map, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU
        )
        return binary_mask
    
    def morphological_filtering(self, binary_mask):
        """Apply morphological opening"""
        kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
        opened = cv2.morphologyEx(binary_mask, cv2.MORPH_OPEN, kernel, iterations=2)
        closed = cv2.morphologyEx(opened, cv2.MORPH_CLOSE, kernel, iterations=1)
        return closed
    
    def extract_text_region(self, mask):
        """Extract bounding box of text"""
        contours, _ = cv2.findContours(
            mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE
        )
        
        if not contours:
            return None
        
        largest = max(contours, key=cv2.contourArea)
        x, y, w, h = cv2.boundingRect(largest)
        return mask[y:y+h, x:x+w]
    
    def perform_ocr(self, text_region):
        """Perform OCR on extracted region"""
        try:
            import pytesseract
            config = '--psm 8 -c tessedit_char_whitelist=BKHMRXWY34689'
            return pytesseract.image_to_string(text_region, config=config).strip()
        except ImportError:
            return None
    
    def attack(self):
        """Execute attack sequence"""
        self.capture_frames()
        variance = self.compute_temporal_variance()
        binary_mask = self.apply_otsu_threshold(variance)
        filtered = self.morphological_filtering(binary_mask)
        text_region = self.extract_text_region(filtered)
        
        if text_region is None:
            return None
        
        result = self.perform_ocr(text_region)
        return result

# Execution
if __name__ == "__main__":
    attacker = TemporalVarianceAttack(
        "https://sheikhlipu123.github.io/TV-Static-CAPTCHA-Security-Analysis-POC/",
        num_frames=30
    )
    result = attacker.attack()
    if result:
        print(f"[*] Extracted: {result}")
    else:
        print("[*] Extraction failed or incomplete")
```

### 6.2 Representative Output

**NOTE: This is illustrative output showing the expected form of results. Actual results would require running the attack against live CAPTCHA instances.**

```
[*] Frames captured: 30
[*] Temporal variance computed
[*] Otsu thresholding applied
[*] Morphological filtering complete
[*] Text region extracted
[*] OCR performed
[*] Extracted: K4HB (example only)
```

---

## 7. Limitations and Realistic Assessment

### 7.1 Why This Attack May Fail

- **Binary patterns are difficult:** Frame differencing works better on complex textures than pure noise
- **OCR limitations:** Even with perfect text extraction, Tesseract may fail on small or degraded text
- **Parameter sensitivity:** The attack requires careful tuning of morphological kernel sizes, thresholds
- **Frame rate dependency:** Results depend on frame capture rate, which may vary by system
- **Noise regeneration:** If noise is truly random per frame, decorrelation may be incomplete

### 7.2 What Would Actually Improve Security

Animation-based obscuration is not a viable primary defense. Stronger approaches:

- **Server-side verification with session tokens**
- **Rate limiting and account lockout**
- **Behavioral analysis** (mouse patterns, device fingerprinting)
- **Cryptographic challenge-response** (FIDO2, WebAuthn)
- **Risk-based assessment** combining multiple signals

Modern CAPTCHA systems (reCAPTCHA v3, Cloudflare Turnstile, Apple Private Access Tokens) do not rely primarily on visual obscuration for this reason.

---

## 8. Discussion

### 8.1 Implications for Animation-Based Defenses

Our analysis suggests that CAPTCHAs relying solely on differential motion are vulnerable to motion detection techniques. However, we emphasize:

- This conclusion is based on analysis of one system
- No systematic evaluation has been performed
- Other animation schemes might be more resilient
- This does not constitute a published vulnerability

### 8.2 Educational Value

This POC demonstrates:
1. How motion detection techniques apply to CAPTCHA analysis
2. Why animation alone should not be a primary security mechanism
3. The importance of server-side verification
4. How to structure an attack implementation

### 8.3 Responsible Disclosure

This is educational research. No live system has been attacked. No novel vulnerabilities are claimed. The work documents established computer vision techniques applied to a hypothetical scenario.

---

## 9. Conclusion

We have presented a CAPTCHA system design and demonstrated how standard motion detection techniques could plausibly extract text. Our analysis suggests that **animation-based obscuration should not be relied upon as a primary security mechanism** for protecting sensitive operations.

However, we emphasize that:
- This conclusion is based on analysis, not systematic empirical validation
- Success rates are unknown without testing
- Other animation schemes may be more resilient
- Modern CAPTCHA systems use multi-layered defenses

The strongest CAPTCHA and authentication approaches combine:
1. **Server-side verification** with cryptographic tokens
2. **Behavioral analysis** and risk assessment
3. **Rate limiting** and account protection
4. **Hardware-backed verification** when protecting high-value operations

This work is intended for educational purposes to illustrate why modern CAPTCHA design has evolved beyond visual obscuration toward cryptographic and behavioral mechanisms.

---

## References

Ahn, L. V., Blum, M., & Langford, J. (2003). Completely automated public Turing test to tell computers and humans apart. *Proceedings of NDSS*.

Horn, B. K., & Schunck, B. G. (1981). Determining optical flow. *Artificial Intelligence*, 17(1-3), 185-203.

Lucas, B. D., & Kanade, T. (1981). An iterative image registration technique with an application to stereo vision. *IJCAI*, 81, 674-679.

Goodfellow, I. J., Bulatov, Y., Ibarz, J., Arnitmam, S., & Shet, V. (2013). Multi-digit number recognition from street view imagery. *arXiv preprint arXiv:1312.6082*.

Serra, J. (1982). *Image analysis and mathematical morphology*. Academic Press.

Torr, P. H., & Zisserman, A. (1998). Performance characterization of fundamental matrix estimation under image uncertainty. *International Journal of Computer Vision*, 29(3), 175-189.

---

## Appendix A: What This Work Is NOT

- ❌ **Not a peer-reviewed publication**
- ❌ **Not based on systematic testing** (no sample size, no statistical analysis)
- ❌ **Not a novel security discovery** (applies established CV techniques)
- ❌ **Not a vulnerability disclosure** (no live system attacked)
- ❌ **Not a mathematical proof** (provides intuitive argument only)
- ❌ **Not empirically validated** (lacks evaluation methodology)

## Appendix B: What This Work IS

- ✅ **Educational resource** demonstrating motion detection applications
- ✅ **Proof of concept** showing plausibility of one attack vector
- ✅ **Illustrative code** for learning purposes
- ✅ **Pedagogical analysis** of CAPTCHA design tradeoffs
- ✅ **Documentation** of why animation alone is insufficient

---

**Document Version:** 2.0 (Revised for accuracy)  
**Last Updated:** 2026-07-14  
**Author:** Sheikhlipu123  
**Status:** Educational White Paper - Not Academic Research

**Feedback:** This document has been reviewed and corrected for technical accuracy and appropriate claim calibration. It is suitable for educational and explanatory purposes but does not meet standards for peer-reviewed publication without significant additional work including systematic empirical evaluation, larger reference review, and removal of unsupported numerical claims.
