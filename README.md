# rainbowwavytexteffect
Rainbow wavy text effect with HTML, CSS and JS.
![me](https://github.com/johnhadless1/rainbowwavytexteffect/blob/main/example.gif)

For full example/demo, download the _rainbowwavyexample.html_ to see the effect in action.

> [!NOTE]
> This was been vibe coded by Claude, then fixed by local LLM gemma4.

## For easy copy-pasting:

### CSS
```
/* Core Wavy Logic */
.wavy-text {
  font-weight: 900;
  font-family: 'Arial Black', 'Impact', sans-serif; /* Or your own font */
  white-space: nowrap;
  line-height: 1.6;
  display: inline-block;
}

.wavy-text span {
  display: inline-block;
  background-image: linear-gradient(to right,
    #ff0000, #ff8800, #ffee00, #00dd66, #00aaff, #aa00ff, #ff0088, #ff0000
  );
  /* These variables are calculated and set by the JavaScript */
  background-size: var(--word-width, 600px) 100%;
  background-position-x: var(--bg-offset-x, 0px);
  
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  
  animation:
    wave var(--dur, 1s) ease-in-out infinite,
    grad-shift var(--grad-dur, 3s) linear infinite;
  animation-delay: var(--wave-delay, 0s), 0s;
}

@keyframes wave {
  0%, 100% { transform: translateY(var(--wave-length, 22px)); }
  50% { transform: translateY(var(--wave-height, -22px)); }
}

@keyframes grad-shift {
  from { background-position-x: var(--bg-offset-x, 0px); }
  to   { background-position-x: calc(var(--bg-offset-x, 0px) - var(--word-width, 600px)); }
}
```

### JS (Just put it in <script> tag.)
```
const WavyText = {
  init() {
    // 1. Process all existing elements
    document.querySelectorAll('.wavy-text').forEach(el => this.apply(el));
    
    // 2. Watch for new elements added to the page dynamically
    new MutationObserver(mutations => {
      mutations.forEach(m => {
        m.addedNodes.forEach(node => {
          if (node.nodeType === 1) {
            if (node.classList.contains('wavy-text')) this.apply(node);
            node.querySelectorAll?.('.wavy-text').forEach(child => this.apply(child));
          }
        });
      });
    }).observe(document.body, { childList: true, subtree: true });
  },

  apply(el) {
    if (el.dataset.wavyInitialized) return;

    const text = el.textContent.trim();
    const STAGGER = 0.08; // The "gap" in time between letters jumping

    // Split text into <span>s so we can animate them individually
    el.innerHTML = [...text].map((c, i) =>
      `<span data-i="${i}">${c === ' ' ? '\u00a0' : c}</span>`
    ).join('');

    requestAnimationFrame(() => {
      const pr = el.getBoundingClientRect();
      const ww = pr.width;
      
      el.querySelectorAll('span').forEach((s, i) => {
        const r = s.getBoundingClientRect();
        const ox = r.left - pr.left; // Calculate letter's position relative to start
        
        s.style.setProperty('--word-width', `${ww}px`);
        s.style.setProperty('--bg-offset-x', `${-ox}px`);
        s.style.setProperty('--wave-delay', `${i * STAGGER}s`);
      });
      el.dataset.wavyInitialized = "true";
    });
  }
};

WavyText.init();
```

### HTML (example)
```
<h1 class="wavy-text">HELLO WORLD</h1>

<!-- --dur = speed, --wave-height/length = jump distance -->
<span class="wavy-text" style="--dur: 0.4s; --wave-height: -5px; --wave-length: 5px; font-size: 20px;">
  TINY WAVE
</span>
```
