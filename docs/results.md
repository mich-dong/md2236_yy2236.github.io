---
title: Results
nav_order: 3
---

# Results

At 30 FPS, we are able to simulate a max of 800 balls before frame rate drops. However around 200 balls can be displayed before the tree graph overflows.

<p align="center">
  <a href="{{ "/assets/images/Screen.png" | relative_url }}">
    <img src="{{ "/assets/images/Screen.png" | relative_url }}" alt="Screenshot" width="70%">
  </a>
</p>

Throughout testing, the system exhibited stable and responsive behavior with no visible flicker, tearing, or hesitation during normal operation. VGA timing remained consistent, and drawing was synchronized to the display refresh to avoid artifacts. User interactions through the potentiometer sliders resulted in immediate and smooth parameter updates, demonstrating good interactivity. The use of concurrent execution which included separating simulation updates, ADC input handling, and VGA rendering into different protothreads and cores, we were able to allow the system to remain responsive even as computational load increased.

The system maintained reliable timing and signal generation across subsystems. The VGA output adhered to expected horizontal and vertical synchronization timing, producing a stable display with consistent geometry. Audio feedback generated during infection events produced audible tones at the intended frequencies, with smooth envelope shaping and no noticeable distortion. While the epidemiological model is not intended to be numerically predictive of real-world outbreaks, the simulation accurately reflects the designed probabilistic rules and timing relationships, and parameter changes consistently produce the expected qualitative effects.

Safety was explicitly enforced through both hardware and enclosure design choices. All electronics are fully contained within a compact enclosure, preventing users from accidentally touching exposed pins or circuitry during operation. Only the necessary external connections (power, video, and audio outputs) are accessible, and these require no internal access to use. All interactive controls are limited to external sliders, which are soldered securely to prevent loosening or shorting. The enclosure was sized to tightly fit the boards, preventing internal movement or strain on connections during handling or transport.

The device was designed for ease of use by both the developers and other users. No prior hardware or software knowledge is required to operate the system: users simply connect the required cables and adjust sliders to explore different epidemic parameters. During informal testing, users were able to understand the effects of infection radius, movement probability, and social distancing through visual feedback alone, without additional explanation. The combination of clear color coding, real-time graphs, and physical controls resulted in an intuitive and accessible interface.

Overall, the system demonstrates strong performance, stable real-time visualization, and safe, user-friendly operation. While visual scalability is limited by the available screen space for the transmission tree, the simulation itself remains performant well beyond the range required for effective demonstration of epidemic dynamics.
