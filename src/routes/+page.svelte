<script lang="ts">
  import { onMount } from 'svelte';
  import { base } from '$app/paths';
  import { BookOpen, BriefcaseBusiness, Github, Linkedin, ShieldCheck, Tag } from '@lucide/svelte';

  let motionEnabled = $state(false);
  let markPath = $state('');
  type LogoAnimation = 'ripple' | 'ascii' | 'flip';
  const logoAnimations: LogoAnimation[] = ['ripple', 'ascii', 'flip'];
  let logoAnimation = $state<LogoAnimation>('ripple');
  let donutFrame = $state('');
  let flipPath = $state('');
  let wordmarkTime = $state(0);
  let syncMotion = () => {};
  let selectNextLogo = () => {};

  function advanceLogoAnimation(): void {
    selectNextLogo();
  }
  const blocks = [
    '   ████████  ',
    '  ███    ███ ',
    ' ███      ███',
    '  ███    ███ ',
    '   ████████  '
  ].flatMap((row, y) => [...row].flatMap((cell, x) => cell === '█' ? [{ x, y }] : []));
  const wordmarkRows = [
    ' ███  ███   ██   ███  ███',
    '█  █  █  █ █  █ █    █',
    ' ███  █    ████ █    ███',
    '   █  █    █  █ █    █',
    '███   █    █  █  ███  ███  █'
  ];
  const wordmarkWidth = Math.max(...wordmarkRows.map(row => row.length));
  const wordmarkCells = wordmarkRows.map(row => Array.from({ length: wordmarkWidth }, (_, x) => row[x] ?? ' '));
  const waveColors = ['var(--color-wave-coral)', 'var(--color-wave-teal)', 'var(--color-wave-gold)', 'var(--color-wave-lavender)'];
  function wordmarkColor(x: number, y: number): string | undefined {
    if (!motionEnabled) return undefined;
    const pass = Math.floor(wordmarkTime / 6000);
    const progress = (wordmarkTime % 6000) / 6000;
    const head = (pass % 2 === 0 ? progress : 1 - progress) * (wordmarkWidth + 10) - 5;
    const edge = Math.round(head + Math.sin(y * 1.1) * 1.5);
    return Math.abs(x - edge) <= 2 ? waveColors[pass % waveColors.length] : undefined;
  }
  const waveOffset = (x: number, time: number) => Math.round(Math.sin(x * 0.9 - time) * 2) / 4;
  const staticMark = 'M3 0H11V1H12V2H13V3H12V4H11V5H3V4H2V3H1V2H2V1H3Z M5 1H9V2H10V3H9V4H5V3H4V2H5Z';

  function renderMark(time: number): string {
    return blocks.map(({ x, y }) => {
      const offset = waveOffset(x, time);
      return `M${x} ${y + offset}h1v1h-1Z`;
    }).join(' ');
  }

  function renderFlip(progress: number): string {
    const scale = Math.cos(2 * Math.PI * progress * progress * (3 - 2 * progress));
    return blocks.map(({ x, y }) => `M${x} ${2 + Math.round((y - 2) * scale)}h1v1h-1Z`).join(' ');
  }

  onMount(() => {
    const preference = window.matchMedia('(prefers-reduced-motion: reduce)');
    const width = 26, height = 10;
    const depth = new Float64Array(width * height);
    const cells = Array<string>(width * height);
    const shades = '.,-~:;=!*#$@';
    const samples: [number, number, number, number, number, number][] = [];
    for (let theta = 0; theta < Math.PI * 2; theta += 0.14) {
      const ct = Math.cos(theta), st = Math.sin(theta);
      for (let phi = 0; phi < Math.PI * 2; phi += 0.08) {
        const cp = Math.cos(phi), sp = Math.sin(phi);
        samples.push([(2 + ct) * cp, (2 + ct) * sp, st, ct * cp, ct * sp, st]);
      }
    }

    function renderDonut(seconds: number): string {
      depth.fill(Infinity);
      cells.fill(' ');
      const a = 0.6 + seconds * 0.45, b = seconds * 0.25;
      const ca = Math.cos(a), sa = Math.sin(a), cb = Math.cos(b), sb = Math.sin(b);
      for (const [x, y, z, nx, ny, nz] of samples) {
        const ry = y * ca - z * sa, rz = y * sa + z * ca;
        const px = x * cb - ry * sb, py = x * sb + ry * cb;
        const distance = 5 + rz;
        const col = Math.round(12.5 + 14 * px / distance);
        const row = Math.round(4.5 - 5.6 * py / distance);
        if (col < 0 || col >= width || row < 0 || row >= height) continue;
        const index = row * width + col;
        if (distance >= depth[index]) continue;
        depth[index] = distance;
        const nry = ny * ca - nz * sa, nrz = ny * sa + nz * ca;
        const npy = nx * sb + nry * cb;
        const light = Math.max(0, Math.min(1, 0.15 + (npy - nrz) / Math.SQRT2));
        cells[index] = shades[Math.round(light * (shades.length - 1))];
      }
      let frame = '';
      for (let row = 0; row < height; row++) {
        if (row) frame += '\n';
        for (let col = 0; col < width; col++) frame += cells[row * width + col];
      }
      return frame;
    }

    let elapsed = 0;
    let startedAt: number | null = null;
    let request: number | null = null;
    let lastFrame = -Infinity;
    let modeStarted = 0;
    const activeTime = (now: number) => elapsed + (startedAt === null ? 0 : now - startedAt);
    function renderLogo(time: number) {
      const seconds = Math.max(0, time - modeStarted) / 1000;
      switch (logoAnimation) {
        case 'ripple':
          markPath = renderMark(seconds);
          break;
        case 'ascii':
          donutFrame = renderDonut(seconds);
          break;
        case 'flip': {
          const progress = Math.max(0, Math.min(1, (seconds % 3 - 0.8) / 1.4));
          flipPath = renderFlip(progress);
          break;
        }
      }
    }
    selectNextLogo = () => {
      if (!motionEnabled) return;
      const time = activeTime(performance.now());
      logoAnimation = logoAnimations[(logoAnimations.indexOf(logoAnimation) + 1) % logoAnimations.length];
      modeStarted = time;
      renderLogo(time);
    };
    function tick(now: number) {
      request = null;
      const time = activeTime(now);
      if (now - lastFrame >= 50) {
        renderLogo(time);
        wordmarkTime = time;
        lastFrame = now;
      }
      request = window.requestAnimationFrame(tick);
    }
    syncMotion = () => {
      const now = performance.now();
      const running = motionEnabled && !document.hidden;
      if (!running && startedAt !== null) {
        elapsed = activeTime(now);
        startedAt = null;
      } else if (running && startedAt === null) {
        startedAt = now;
        lastFrame = -Infinity;
      }
      if (!running && request !== null) {
        cancelAnimationFrame(request);
        request = null;
      } else if (running && request === null) {
        request = requestAnimationFrame(tick);
      }
    };
    const onPreferenceChange = () => {
      if (preference.matches) {
        motionEnabled = false;
        syncMotion();
        elapsed = 0;
        markPath = '';
        wordmarkTime = 0;
        modeStarted = 0;
        donutFrame = '';
        flipPath = '';
      } else {
        motionEnabled = true;
        renderLogo(0);
        syncMotion();
      }
    };
    motionEnabled = !preference.matches;
    if (motionEnabled) renderLogo(0);
    syncMotion();
    preference.addEventListener('change', onPreferenceChange);
    document.addEventListener('visibilitychange', syncMotion);
    return () => {
      preference.removeEventListener('change', onPreferenceChange);
      document.removeEventListener('visibilitychange', syncMotion);
      if (request !== null) cancelAnimationFrame(request);
      syncMotion = () => {};
      selectNextLogo = () => {};
    };
  });
</script>

<div class="landing" class:motion-enabled={motionEnabled}>
<div class="identity">
  <button
    type="button"
    class="logo"
    disabled={!motionEnabled}
    aria-label={`Grace logo: ${logoAnimation === 'ascii' ? 'ASCII donut' : logoAnimation}. Change animation`}
    onclick={advanceLogoAnimation}
  >
    {#if motionEnabled && logoAnimation === 'ascii' && donutFrame}
      <pre class="ascii-logo" aria-hidden="true">{donutFrame}</pre>
    {:else}
      <svg
        viewBox="0 0 13 5"
        preserveAspectRatio="none"
        aria-hidden="true"
      >
        <path fill="currentColor" fill-rule={logoAnimation === 'flip' && motionEnabled ? 'nonzero' : 'evenodd'} d={motionEnabled && logoAnimation === 'flip' && flipPath ? flipPath : motionEnabled && logoAnimation === 'ripple' && markPath ? markPath : staticMark} />
      </svg>
    {/if}
  </button>
  <h1 aria-label="grace."><span class="wordmark" aria-hidden="true">{#each wordmarkCells as row, y}<span class="wordmark-row">{#each row as cell, x}<span class="wordmark-cell" style:color={wordmarkColor(x, y)}>{cell}</span>{/each}</span>{/each}</span></h1>
</div>
<p class="tagline">pomodoro timer &amp; app/website blocker in 1</p>
  <section aria-labelledby="project-heading">
    <h2 id="project-heading">project</h2>
    <ul>
      <li><a class="link-row" href="https://github.com/samir-scripts/grace"><span class="row-label"><Github aria-hidden="true" />repository</span><span aria-hidden="true">›</span></a></li>
      <li><a class="link-row" href="https://github.com/samir-scripts/grace/releases"><span class="row-label"><Tag aria-hidden="true" />releases</span><span aria-hidden="true">›</span></a></li>
      <li><a class="link-row" href="https://github.com/samir-scripts/grace/blob/main/README.md"><span class="row-label"><BookOpen aria-hidden="true" />documentation</span><span aria-hidden="true">›</span></a></li>
    </ul>
  </section>
  <section aria-labelledby="developer-heading">
    <h2 id="developer-heading">developer</h2>
    <ul>
      <li><div class="link-row"><span class="row-label"><BriefcaseBusiness aria-hidden="true" />portfolio</span><span class="pending">pending</span></div></li>
      <li><a class="link-row" href="https://github.com/samir-scripts"><span class="row-label"><Github aria-hidden="true" />github</span><span aria-hidden="true">›</span></a></li>
      <li><a class="link-row" href="https://www.linkedin.com/in/samir-katakamsetty1234/"><span class="row-label"><Linkedin aria-hidden="true" />linkedin</span><span aria-hidden="true">›</span></a></li>
    </ul>
  </section>
  <section aria-labelledby="privacy-heading">
    <h2 id="privacy-heading">privacy</h2>
    <ul>
      <li><a class="link-row" href={`${base}/privacy/`}><span class="row-label"><ShieldCheck aria-hidden="true" />privacy policy</span><span aria-hidden="true">›</span></a></li>
    </ul>
  </section>
</div>

<style>
  .landing { width: 100%; max-width: 640px; min-width: 0; }
  .identity { display: flex; align-items: center; justify-content: center; gap: clamp(12px, 3vw, 24px); text-align: center; }
  .logo { display: grid; width: 13ch; height: 5em; flex: 0 0 auto; color: var(--color-focus); font-family: inherit; font-size: clamp(12px, 4vw, 20px); line-height: 1; user-select: none; }
  .logo { appearance: none; padding: 0; border: 0; background: transparent; box-shadow: none; opacity: 1; cursor: pointer; }
  .logo:disabled { cursor: default; }
  .logo:focus:not(:focus-visible) { outline: none; }
  .logo:focus-visible { outline: 2px solid var(--color-foreground); outline-offset: 4px; }
  .ascii-logo { width: 26ch; height: 10em; margin: 0; padding: 0; font-family: inherit; font-size: 50%; font-weight: 400; line-height: 1; white-space: pre; text-align: left; }
  .logo svg { width: 100%; height: 100%; overflow: visible; }
  .tagline { min-height: 76px; margin: 0; display: flex; justify-content: center; align-items: center; padding: 0 8px; color: var(--color-muted); font-size: 12px; line-height: 1.5; text-align: center; }
  h1 { margin: 0; line-height: 1; }
  h1 .wordmark { display: inline-block; white-space: pre; font-family: inherit; font-weight: 400; line-height: 1; font-size: clamp(8px, 2.6vw, 16px); text-align: left; }
  .wordmark-row { display: block; height: 1em; white-space: pre; }
  .wordmark-cell { display: inline-block; width: 1ch; }
  section { border-top: 1px solid var(--color-rule); }
  h2 { margin: 0; padding: 12px 12px 4px; color: var(--color-muted); font-size: 12px; line-height: 1.5; font-weight: 400; }
  ul { margin: 0; padding: 0; list-style: none; }
  .link-row { display: flex; align-items: center; justify-content: space-between; gap: 16px; min-height: 44px; padding: 10px 12px; font-size: 14px; line-height: 1.5; }
  .row-label { display: inline-flex; align-items: center; gap: 12px; min-width: 0; }
  .row-label :global(svg) { width: 16px; height: 16px; flex: 0 0 auto; }
  .pending { color: var(--color-muted); font-size: 12px; line-height: 1.5; }
  a.link-row { color: inherit; text-decoration: none; cursor: pointer; }
  a.link-row:hover, a.link-row:focus-visible { background: var(--color-row-hover); }
  a.link-row:focus-visible { outline: 2px solid var(--color-foreground); outline-offset: -2px; }
  @media (max-width: 479px) {
    .logo { font-size: 12px; }
    h1 .wordmark { font-size: 8px; }
    .identity { gap: 12px; }
  }
</style>
