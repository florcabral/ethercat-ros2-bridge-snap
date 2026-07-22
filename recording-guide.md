<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>EtherCAT ROS 2 Bridge — Recording Guide</title>
  <style>
    :root {
      color-scheme: dark;
      --bg: #080d18;
      --panel: #101827;
      --panel-2: #151f31;
      --line: #293752;
      --text: #eef4ff;
      --muted: #a9b6ca;
      --accent: #77bdfb;
      --green: #4ade80;
      --yellow: #facc15;
      --red: #fb7185;
      --host: #60a5fa;
      --vm: #c084fc;
      --gui: #34d399;
      --shadow: 0 18px 55px rgba(0, 0, 0, .28);
    }

    * { box-sizing: border-box; }
    html { scroll-behavior: smooth; }
    body {
      margin: 0;
      background:
        radial-gradient(circle at 8% 2%, rgba(96, 165, 250, .13), transparent 27rem),
        radial-gradient(circle at 90% 12%, rgba(192, 132, 252, .10), transparent 25rem),
        var(--bg);
      color: var(--text);
      font: 16px/1.55 Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    a { color: var(--accent); }
    button, input { font: inherit; }

    .topbar {
      position: sticky;
      top: 0;
      z-index: 20;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 1rem;
      padding: .8rem max(1rem, calc((100vw - 1240px) / 2));
      border-bottom: 1px solid rgba(119, 189, 251, .18);
      background: rgba(8, 13, 24, .88);
      backdrop-filter: blur(16px);
    }

    .topbar strong { letter-spacing: .01em; }
    .legend { display: flex; gap: .5rem; flex-wrap: wrap; }

    .badge {
      display: inline-flex;
      align-items: center;
      gap: .4rem;
      padding: .25rem .62rem;
      border: 1px solid currentColor;
      border-radius: 999px;
      font-size: .75rem;
      font-weight: 800;
      letter-spacing: .06em;
      text-transform: uppercase;
      white-space: nowrap;
    }
    .badge.host { color: var(--host); background: rgba(96, 165, 250, .1); }
    .badge.vm { color: var(--vm); background: rgba(192, 132, 252, .1); }
    .badge.gui { color: var(--gui); background: rgba(52, 211, 153, .1); }

    main {
      width: min(1240px, calc(100% - 2rem));
      margin: 0 auto;
      padding: 2rem 0 5rem;
      display: grid;
      grid-template-columns: minmax(0, 1fr) 270px;
      gap: 1.5rem;
      align-items: start;
    }

    .content { min-width: 0; }
    .hero {
      padding: clamp(1.4rem, 4vw, 2.6rem);
      border: 1px solid var(--line);
      border-radius: 24px;
      background: linear-gradient(145deg, rgba(21, 31, 49, .96), rgba(11, 18, 31, .96));
      box-shadow: var(--shadow);
    }
    .eyebrow {
      margin: 0 0 .5rem;
      color: var(--accent);
      font-weight: 800;
      letter-spacing: .12em;
      text-transform: uppercase;
      font-size: .76rem;
    }
    h1 { margin: 0; font-size: clamp(2rem, 5vw, 3.5rem); line-height: 1.06; }
    .hero p { max-width: 760px; margin: 1rem 0 0; color: var(--muted); font-size: 1.08rem; }

    .status-strip {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: .75rem;
      margin-top: 1.4rem;
    }
    .status-item {
      padding: .8rem 1rem;
      border: 1px solid var(--line);
      border-radius: 14px;
      background: rgba(8, 13, 24, .5);
    }
    .status-item b { display: block; color: var(--green); }
    .status-item span { color: var(--muted); font-size: .85rem; }

    section { scroll-margin-top: 5.2rem; }
    .section-heading { margin: 2.3rem 0 1rem; }
    .section-heading h2 { margin: 0; font-size: 1.7rem; }
    .section-heading p { margin: .35rem 0 0; color: var(--muted); }

    .callout {
      margin: 1rem 0;
      padding: 1rem 1.1rem;
      border: 1px solid rgba(250, 204, 21, .35);
      border-left: 4px solid var(--yellow);
      border-radius: 12px;
      background: rgba(250, 204, 21, .07);
      color: #fff7c2;
    }
    .callout.good {
      border-color: rgba(74, 222, 128, .35);
      border-left-color: var(--green);
      background: rgba(74, 222, 128, .07);
      color: #d8ffe6;
    }
    .callout strong { color: inherit; }

    .layout-diagram {
      display: grid;
      grid-template-columns: 1fr 1.65fr;
      min-height: 300px;
      border: 2px solid #51617d;
      border-radius: 18px;
      overflow: hidden;
      background: #0b1220;
      box-shadow: var(--shadow);
    }
    .layout-diagram > div {
      display: grid;
      place-items: center;
      padding: 1rem;
      text-align: center;
      font-weight: 800;
    }
    .plot-area { color: #bae6fd; background: linear-gradient(145deg, #10263c, #0e1728); }
    .left-stack { display: grid !important; grid-template-rows: 1.1fr 1fr; padding: 0 !important; }
    .terminal-area { color: #e9d5ff; border-right: 2px solid #51617d; border-bottom: 2px solid #51617d; background: #211532; }
    .camera-area { color: #bbf7d0; border-right: 2px solid #51617d; background: #102a22; }
    .layout-diagram small { display: block; margin-top: .3rem; color: var(--muted); font-weight: 500; }

    .step {
      position: relative;
      margin: 1rem 0;
      padding: 1.25rem;
      border: 1px solid var(--line);
      border-radius: 18px;
      background: linear-gradient(145deg, rgba(16, 24, 39, .98), rgba(12, 19, 32, .98));
      box-shadow: 0 10px 30px rgba(0, 0, 0, .18);
    }
    .step-header {
      display: flex;
      align-items: flex-start;
      justify-content: space-between;
      gap: 1rem;
      margin-bottom: .75rem;
    }
    .step-title { display: flex; align-items: flex-start; gap: .8rem; }
    .step-number {
      flex: 0 0 auto;
      display: grid;
      place-items: center;
      width: 2rem;
      height: 2rem;
      border-radius: 9px;
      background: #24344f;
      color: #fff;
      font-weight: 900;
    }
    .step h3 { margin: .05rem 0 0; font-size: 1.12rem; }
    .step p { color: var(--muted); margin: .55rem 0; }

    .command {
      position: relative;
      margin-top: .8rem;
      border: 1px solid #354764;
      border-radius: 13px;
      overflow: hidden;
      background: #050a13;
    }
    pre {
      margin: 0;
      padding: 1rem 5.5rem 1rem 1rem;
      overflow-x: auto;
      color: #e9f2ff;
      font: 500 .91rem/1.55 "JetBrains Mono", "Cascadia Code", "Ubuntu Mono", Consolas, monospace;
      white-space: pre-wrap;
      overflow-wrap: anywhere;
    }
    .copy {
      position: absolute;
      top: .62rem;
      right: .62rem;
      min-width: 4.25rem;
      padding: .42rem .65rem;
      border: 1px solid #466184;
      border-radius: 8px;
      background: #182842;
      color: #dcebff;
      cursor: pointer;
      font-weight: 800;
      font-size: .78rem;
      transition: .15s ease;
    }
    .copy:hover { transform: translateY(-1px); background: #213657; border-color: var(--accent); }
    .copy.copied { color: #07110b; background: var(--green); border-color: var(--green); }

    .expected {
      margin-top: .75rem;
      padding: .75rem .9rem;
      border-radius: 10px;
      background: rgba(74, 222, 128, .07);
      color: #caffdb;
      border: 1px solid rgba(74, 222, 128, .2);
      font-family: "JetBrains Mono", "Ubuntu Mono", monospace;
      font-size: .86rem;
      white-space: pre-wrap;
      overflow-wrap: anywhere;
    }
    .expected::before {
      content: "EXPECTED  ";
      color: var(--green);
      font-weight: 900;
      font-family: Inter, sans-serif;
      font-size: .68rem;
      letter-spacing: .08em;
    }

    .action-list { margin: .7rem 0 0; padding-left: 1.35rem; }
    .action-list li { margin: .35rem 0; }
    kbd {
      padding: .15rem .45rem;
      border: 1px solid #52647f;
      border-bottom-width: 3px;
      border-radius: 6px;
      background: #1a2639;
      font: 700 .85rem ui-monospace, monospace;
    }

    aside {
      position: sticky;
      top: 5rem;
      padding: 1rem;
      border: 1px solid var(--line);
      border-radius: 18px;
      background: rgba(16, 24, 39, .94);
      box-shadow: var(--shadow);
    }
    aside h2 { margin: 0 0 .7rem; font-size: 1.05rem; }
    .toc { display: grid; gap: .25rem; margin-bottom: 1rem; }
    .toc a {
      padding: .35rem .45rem;
      border-radius: 7px;
      color: var(--muted);
      text-decoration: none;
      font-size: .9rem;
    }
    .toc a:hover { color: #fff; background: #1c2a40; }
    .checklist { display: grid; gap: .55rem; padding-top: .85rem; border-top: 1px solid var(--line); }
    .checklist label { display: flex; gap: .55rem; align-items: flex-start; color: var(--muted); font-size: .88rem; cursor: pointer; }
    .checklist input { margin-top: .23rem; accent-color: var(--green); }
    .reset {
      width: 100%;
      margin-top: .8rem;
      padding: .5rem;
      border: 1px solid #3d4b63;
      border-radius: 8px;
      color: var(--muted);
      background: transparent;
      cursor: pointer;
    }
    .reset:hover { color: #fff; border-color: var(--accent); }

    .final-card {
      margin-top: 1rem;
      padding: 1.2rem;
      border: 1px solid rgba(74, 222, 128, .3);
      border-radius: 16px;
      background: linear-gradient(145deg, rgba(74, 222, 128, .1), rgba(16, 24, 39, .95));
    }
    .final-card h3 { margin: 0 0 .5rem; }

    footer { margin-top: 2rem; color: var(--muted); text-align: center; font-size: .84rem; }

    @media (max-width: 930px) {
      main { grid-template-columns: 1fr; }
      aside { position: static; }
      .toc { grid-template-columns: repeat(2, 1fr); }
    }
    @media (max-width: 650px) {
      .topbar { align-items: flex-start; flex-direction: column; }
      .status-strip { grid-template-columns: 1fr; }
      .layout-diagram { grid-template-columns: 1fr; }
      .left-stack { grid-template-rows: 1fr 1fr; }
      .terminal-area, .camera-area { border-right: 0; border-top: 2px solid #51617d; }
      .step-header { flex-direction: column; }
      pre { padding-right: 1rem; padding-top: 3.6rem; }
      .copy { left: .62rem; right: auto; }
    }
  </style>
</head>
<body>
  <header class="topbar">
    <strong>EtherCAT ROS 2 Bridge · Recording Guide</strong>
    <div class="legend" aria-label="Location legend">
      <span class="badge host">Host</span>
      <span class="badge vm">Ubuntu 26.04 VM</span>
      <span class="badge gui">GUI action</span>
    </div>
  </header>

  <main>
    <div class="content">
      <section class="hero" id="start">
        <p class="eyebrow">ROBENG-1886 · final demo</p>
        <h1>Physical EtherCAT → strict snap → ROS 2 → PlotJuggler</h1>
        <p>Use this page while recording. Run every command separately, pause so its output is readable, and use the location badge to avoid running host commands inside the VM.</p>
        <div class="status-strip">
          <div class="status-item"><b>Bridge snap</b><span>Runs inside ec-pt</span></div>
          <div class="status-item"><b>PlotJuggler snap</b><span>Runs on the host</span></div>
          <div class="status-item"><b>Recording</b><span>OBS · separate PipeWire sources</span></div>
        </div>
      </section>

      <section id="layout">
        <div class="section-heading">
          <h2>1. Configure OBS and the layout</h2>
          <p>Capture each application as a separate OBS source. This keeps the guide and other desktop windows out of the final video.</p>
        </div>
        <div class="layout-diagram" aria-label="Recommended screen layout">
          <div class="left-stack">
            <div class="terminal-area">Terminal 1<br><small>Top-left · LIVE dashboard</small></div>
            <div class="camera-area">EasyCAT webcam<br><small>Bottom-left · dials, board and hand</small></div>
          </div>
          <div class="plot-area">PlotJuggler<br><small>Right 60–65% · both live curves</small></div>
        </div>
        <div class="callout"><strong>Verified host:</strong> Ubuntu GNOME is running Wayland with OBS 30.0.2 and Intel Iris Plus 640 graphics. Xcomposite is therefore not the capture path for this session.</div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Set the video and recording format</h3></div></div>
            <span class="badge gui">OBS</span>
          </div>
          <ul class="action-list">
            <li><strong>Settings → Video:</strong> base and output resolution <strong>1920×1080</strong>, <strong>30 FPS</strong>.</li>
            <li><strong>Settings → Output:</strong> record as <strong>MKV</strong>. Remux to MP4 after recording if needed.</li>
            <li>Prefer <strong>FFmpeg VAAPI H.264</strong> to use the Intel GPU. If it is unstable, use <strong>x264 · veryfast</strong>.</li>
            <li>Do not use NVENC on this host: no Nvidia GPU is present.</li>
          </ul>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Add separate application sources</h3></div></div>
            <span class="badge gui">OBS</span>
          </div>
          <ol class="action-list">
            <li>Add <strong>Window Capture (PipeWire)</strong>, choose the <strong>PlotJuggler window</strong>, and name it <strong>PlotJuggler</strong>.</li>
            <li>Add another <strong>Window Capture (PipeWire)</strong>, choose the <strong>terminal window</strong>, and name it <strong>Terminal</strong>.</li>
            <li>Add <strong>Video Capture Device (V4L2)</strong> and select <strong>Apple FaceTime HD</strong>.</li>
            <li>Use <strong>1296×736</strong>, <strong>YUYV</strong>, and <strong>30 FPS</strong>. The required MacBookPro14,1 camera driver and firmware are installed.</li>
            <li>Do not select <strong>OBS Virtual Camera</strong>; it is OBS's output-only virtual device, not the built-in camera.</li>
            <li>Keep captured windows open and not minimized. Some applications stop redrawing while minimized.</li>
          </ol>
          <div class="callout"><strong>Wayland note:</strong> the portal picker appears while each source is created. That is expected; it does not prevent a multi-window composition.</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Compose the canvas</h3></div></div>
            <span class="badge gui">OBS</span>
          </div>
          <ul class="action-list">
            <li>Place the terminal at the top-left and the EasyCAT webcam at the bottom-left.</li>
            <li>Place PlotJuggler on the right and give it about 60–65% of the canvas width.</li>
            <li>Extend PlotJuggler from the top to the bottom of the canvas. Hold <kbd>Alt</kbd> and crop its left sidebar if the horizontal window does not fit cleanly.</li>
            <li>Until the webcam is added, the terminal may use the full left column.</li>
            <li>Hold <kbd>Alt</kbd> while dragging a source edge to crop menus or unused space.</li>
            <li>Optional: add short text labels, then group each label with its panel.</li>
          </ul>
          <div class="callout good"><strong>Exact layout for the current 2560×1440 canvas:</strong> open <em>Transform → Edit Transform</em> for each source and enter the values below. Leave rotation at 0 and use Top Left positional alignment.</div>
          <div class="expected">Terminal
Position: 0, 0
Size: 1024 × 858
Crop: left 0 · top 0 · right 651 · bottom 0

EasyCAT Webcam
Position: 0, 858
Size: 1024 × 582
Crop: 0 on every side

PlotJG
Position: 1024, 0
Size: 1536 × 1440
Crop: left 600 · top 0 · right 253 · bottom 0</div>
          <div class="callout"><strong>Why these values fit:</strong> the left column is exactly 40% of the canvas and contains the terminal above the 16:9 webcam. PlotJuggler occupies the full 60% right column; cropping removes its left control panel and excess right edge instead of stretching the graph.</div>
          <ul class="action-list">
            <li>After entering the values, lock all three sources using the padlock icons in the Sources panel.</li>
            <li>Do not reopen the webcam Properties dialog; its `/dev/video0` settings are already configured and working.</li>
          </ul>
        </article>

        <div class="callout"><strong>Terminal 2:</strong> the cleanest option is a second tab in the captured terminal window. Switch tabs briefly with <kbd>Ctrl</kbd> + <kbd>Page Down</kbd>/<kbd>Page Up</kbd> to show OP, 3/3, and `/joint_states`. If using a separate terminal window instead, add it as another PipeWire source and toggle its visibility in OBS.</div>
        <div class="callout good"><strong>You can keep this guide in front:</strong> use its Copy buttons, then focus the terminal and paste. OBS captures the Terminal and PlotJuggler windows directly, so the browser guide is not included. Keep the captured windows open behind the guide; do not minimize them.</div>
        <div class="callout good"><strong>No telemetry text source is required:</strong> the LIVE dashboard already shows A0/A1 and PlotJuggler shows the same `/joint_states` values. Avoid adding duplicate moving text before the final take.</div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">4</span><div><h3>Post-edit camera alternative</h3><p>Use only if the built-in camera framing is impractical during the live take.</p></div></div>
            <span class="badge gui">Optional</span>
          </div>
          <ol class="action-list">
            <li>Record the terminal and PlotJuggler composite first, leaving the bottom-left camera area empty.</li>
            <li>Record a separate close-up of the EasyCAT board at 720p30.</li>
            <li>In OpenShot, place the OBS recording on the lower track and the camera clip on the track above it.</li>
            <li>Use the camera clip's <strong>Transform</strong> control to resize it into the empty bottom-left area. Preserve its aspect ratio.</li>
            <li>Present it as an illustrative close-up unless it was recorded simultaneously; a later camera take cannot truthfully match the exact PlotJuggler timing.</li>
          </ol>
        </article>
      </section>

      <section id="prepare">
        <div class="section-heading">
          <h2>2. Prepare before recording</h2>
          <p>PlotJuggler should already be open and configured. These actions are not part of the final video.</p>
        </div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Prepare PlotJuggler</h3><p>Leave it running while the temporary bridge is stopped.</p></div></div>
            <span class="badge gui">GUI</span>
          </div>
          <ul class="action-list">
            <li>Streamer: <strong>ROS2 Topic Subscriber</strong></li>
            <li>Topic: <strong>/joint_states</strong></li>
            <li>Curves: <strong>easycat_analog_0/position</strong> and <strong>easycat_analog_1/position</strong></li>
            <li>Put both curves on the same graph.</li>
            <li>Set the streaming buffer to <strong>30 sec</strong>.</li>
            <li>Save the layout with <strong>Save data source</strong> enabled.</li>
          </ul>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Only if PlotJuggler is closed</h3><p>Run on the host. Do not run this if the application is already open.</p></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>QT_QPA_PLATFORM=xcb ROS_DOMAIN_ID=0 ROS_AUTOMATIC_DISCOVERY_RANGE=SUBNET RMW_IMPLEMENTATION=rmw_fastrtps_cpp plotjuggler --nosplash --window_title 'EasyCAT /joint_states'</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Stop any temporary bridge</h3><p>Run inside the VM. PlotJuggler may remain open; its curves will pause and recover after the recorded launch.</p></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>pkill -INT -f '[e]asycat_console_monitor|[r]os2 launch easycat_bridge easycat.launch.py' || true</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">4</span><div><h3>Stop any stale ROS CLI daemon</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>snap run ethercat-ros2-bridge.ros2 daemon stop 2&gt;/dev/null || true</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">5</span><div><h3>Confirm the slave returned to PREOP</h3><p>Wait a few seconds after stopping the bridge.</p></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>sudo snap run ethercat-ros2-bridge.ethercat slaves</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">0  0:0  PREOP  +  Generic 32+32 bytes rev 1</div>
        </article>

        <div class="callout good"><strong>Ready:</strong> prepare two tabs in the captured host terminal window, confirm the PlotJuggler and Terminal sources are visible on the OBS canvas, and start recording.</div>
      </section>

      <section id="automated">
        <div class="section-heading">
          <h2>3. Recommended — automated live take</h2>
          <p>Use this instead of copying every command. The runner prints each real command, executes it, shows the acceptance evidence, and controls the LIVE dashboard automatically.</p>
        </div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Start OBS recording</h3><p>Confirm the webcam, terminal, and PlotJuggler are visible and the three sources are locked.</p></div></div>
            <span class="badge gui">OBS</span>
          </div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Run the complete sequence once</h3><p>This is the only command that must be entered for the final take.</p></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>cd ~/Documents/26.10/ethercat/ros2-ethercat-snap &amp;&amp; ./AUTOMATED-RECORDING-DEMO.sh</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Move the physical dials when notified</h3></div></div>
            <span class="badge gui">Physical action</span>
          </div>
          <ol class="action-list">
            <li><strong>LEFT DIAL:</strong> rotate the left dial slowly from low to high and back to the middle.</li>
            <li><strong>RIGHT DIAL:</strong> repeat with the right dial.</li>
            <li><strong>BOTH DIALS:</strong> move both together and then independently.</li>
            <li><strong>HOLD STILL:</strong> return both dials to the middle.</li>
          </ol>
          <div class="callout good"><strong>No keyboard input is needed after launch.</strong> The cues are desktop notifications, so you can concentrate on the board. They are not captured because OBS records the three individual sources rather than the desktop.</div>
          <div class="callout"><strong>Transparent automation:</strong> commands and telemetry are not fabricated. The runner executes the real host and VM commands, waits for a physical `/joint_states` sample, shows OP and WorkingCounter 3/3, and uses the monitor's normal graceful shutdown.</div>
        </article>

        <div class="callout good"><strong>Finish:</strong> when the terminal says <em>Demo complete</em>, stop OBS recording. The remaining manual sections are fallback/reference only.</div>
      </section>

      <section id="terminal1">
        <div class="section-heading">
          <h2>Manual fallback — Terminal 1</h2>
          <p>Use these steps only if the automated live take cannot run.</p>
        </div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Clear Terminal 1</h3></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>clear</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Prove PlotJuggler is an installed snap</h3><p>Pause for two seconds after the output.</p></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>snap list plotjuggler</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">plotjuggler  3.17.2  ...  davide-faconti</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Connect Terminal 1 to ec-pt</h3><p>Run SSH only on the host. The private key does not exist inside the VM.</p></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>ssh -tt -o BatchMode=yes -o IdentitiesOnly=yes -i ~/.ssh/ethercat flor@192.168.122.50</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="callout"><strong>Location check:</strong> host kernel is `6.17.0-35`; VM kernel is `7.0.0-28`. Do not SSH again after entering the VM.</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">4</span><div><h3>Show Ubuntu 26.04</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>cat /etc/os-release | grep PRETTY_NAME</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">5</span><div><h3>Show the bridge and ROS content snaps</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>snap list ethercat-ros2-bridge ros-lyrical-ros-base</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">6</span><div><h3>Show strict snap interfaces</h3><p>Point out the ROS content and EtherCAT custom-device connections.</p></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>snap connections ethercat-ros2-bridge</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">7</span><div><h3>Show the physical slave before launch</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>sudo snap run ethercat-ros2-bridge.ethercat slaves</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">0  0:0  PREOP  +  Generic 32+32 bytes rev 1</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">8</span><div><h3>Start the complete physical demo</h3><p>Keep this terminal running. Do not enter another command here.</p></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>snap run ethercat-ros2-bridge.demo</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">Status: LIVE · A0 and A1 values visible · ROS topic: /joint_states</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">9</span><div><h3>Demonstrate the dials</h3></div></div>
            <span class="badge gui">Physical action</span>
          </div>
          <ol class="action-list">
            <li>Rotate the left dial slowly: low → high → middle.</li>
            <li>Show A0 and the first PlotJuggler curve moving.</li>
            <li>Rotate the right dial slowly: low → high → middle.</li>
            <li>Show A1 and the second PlotJuggler curve moving.</li>
            <li>Rotate both together, then pause for two seconds.</li>
          </ol>
          <div class="callout"><strong>If PlotJuggler does not resume:</strong> click Stop, confirm <em>ROS2 Topic Subscriber</em>, click Start, select `/joint_states`, then click OK.</div>
        </article>
      </section>

      <section id="terminal2">
        <div class="section-heading">
          <h2>Manual fallback — Terminal 2</h2>
          <p>Leave Terminal 1 and the dashboard running. Prefer a second tab in the same captured terminal window and switch to it briefly.</p>
        </div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Connect Terminal 2 to ec-pt</h3></div></div>
            <span class="badge host">Host</span>
          </div>
          <div class="command"><pre><code>ssh -tt -o BatchMode=yes -o IdentitiesOnly=yes -i ~/.ssh/ethercat flor@192.168.122.50</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Show that the slave reached OP</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>sudo snap run ethercat-ros2-bridge.ethercat slaves</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">0  0:0  OP  +  Generic 32+32 bytes rev 1</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Show the complete process domain</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>sudo snap run ethercat-ros2-bridge.ethercat domains</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">Domain0: LogBaseAddr 0x00000000, Size 64, WorkingCounter 3/3</div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">4</span><div><h3>Show one standard ROS JointState message</h3><p>Pause while both names and values are readable.</p></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>snap run ethercat-ros2-bridge.ros2 topic echo --once /joint_states sensor_msgs/msg/JointState</code></pre><button class="copy" type="button">Copy</button></div>
          <div class="expected">name:
- easycat_analog_0
- easycat_analog_1
position:
- &lt;A0 value&gt;
- &lt;A1 value&gt;</div>
        </article>

        <div class="callout good"><strong>Final visual:</strong> return to the dashboard + PlotJuggler layout and move both dials once more. The terminal values and curves should react together.</div>
      </section>

      <section id="finish">
        <div class="section-heading">
          <h2>5. Finish cleanly</h2>
          <p>Stop the bridge before ending the recording.</p>
        </div>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">1</span><div><h3>Stop the dashboard in Terminal 1</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <p>Focus Terminal 1 and press <kbd>Ctrl</kbd> + <kbd>C</kbd>. Wait for the bridge-log path and shell prompt.</p>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">2</span><div><h3>Disconnect Terminal 2</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>exit</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">3</span><div><h3>Disconnect Terminal 1</h3></div></div>
            <span class="badge vm">VM</span>
          </div>
          <div class="command"><pre><code>exit</code></pre><button class="copy" type="button">Copy</button></div>
        </article>

        <article class="step">
          <div class="step-header">
            <div class="step-title"><span class="step-number">4</span><div><h3>Stop OBS recording</h3></div></div>
            <span class="badge gui">GUI</span>
          </div>
          <p>Stop recording only after the bridge exits cleanly. Keep the original recording and export any trimmed copy under a new filename.</p>
        </article>

        <div class="final-card">
          <h3>Acceptance evidence in the final video</h3>
          <ul class="action-list">
            <li>Official `plotjuggler` snap is installed on the host.</li>
            <li>Ubuntu 26.04 VM contains the bridge and ROS content snaps.</li>
            <li>Strict ROS content and EtherCAT custom-device interfaces are connected.</li>
            <li>The physical EtherCAT slave changes from PREOP to OP.</li>
            <li>EtherCAT domain reports WorkingCounter 3/3.</li>
            <li>Dashboard reaches LIVE and reacts to both dials.</li>
            <li>`/joint_states` publishes both analog names and values.</li>
            <li>Both PlotJuggler curves move with the physical dials.</li>
          </ul>
        </div>
      </section>

      <footer>Local recording aid · no repository commit or push required</footer>
    </div>

    <aside>
      <h2>Jump to</h2>
      <nav class="toc" aria-label="Page sections">
        <a href="#layout">1 · Screen layout</a>
        <a href="#prepare">2 · Prepare</a>
        <a href="#automated">3 · Automated take</a>
        <a href="#terminal1">Manual · Terminal 1</a>
        <a href="#terminal2">Manual · Terminal 2</a>
        <a href="#finish">5 · Finish</a>
      </nav>

      <h2>Recording checklist</h2>
      <div class="checklist">
        <label><input type="checkbox" data-check="plot"> PlotJuggler configured</label>
        <label><input type="checkbox" data-check="layout"> Windows arranged</label>
        <label><input type="checkbox" data-check="obs"> OBS window sources ready</label>
        <label><input type="checkbox" data-check="mkv"> MKV · 1080p30 · VA-API ready</label>
        <label><input type="checkbox" data-check="runner"> Automated runner ready</label>
        <label><input type="checkbox" data-check="preop"> Slave visible in PREOP</label>
        <label><input type="checkbox" data-check="live"> Dashboard reaches LIVE</label>
        <label><input type="checkbox" data-check="curves"> Both curves move</label>
        <label><input type="checkbox" data-check="op"> Slave shows OP</label>
        <label><input type="checkbox" data-check="wc"> Domain shows 3/3</label>
        <label><input type="checkbox" data-check="topic"> JointState captured</label>
        <label><input type="checkbox" data-check="stop"> Clean Ctrl-C shutdown</label>
      </div>
      <button class="reset" id="reset-checklist" type="button">Reset checklist</button>
    </aside>
  </main>

  <script>
    const fallbackCopy = (text) => {
      const area = document.createElement('textarea');
      area.value = text;
      area.setAttribute('readonly', '');
      area.style.position = 'fixed';
      area.style.opacity = '0';
      document.body.appendChild(area);
      area.select();
      document.execCommand('copy');
      area.remove();
    };

    document.querySelectorAll('.copy').forEach((button) => {
      button.addEventListener('click', async () => {
        const text = button.closest('.command').querySelector('code').textContent;
        try {
          if (navigator.clipboard && window.isSecureContext) {
            await navigator.clipboard.writeText(text);
          } else {
            fallbackCopy(text);
          }
          button.textContent = 'Copied!';
          button.classList.add('copied');
          window.setTimeout(() => {
            button.textContent = 'Copy';
            button.classList.remove('copied');
          }, 1200);
        } catch (_) {
          fallbackCopy(text);
          button.textContent = 'Copied!';
          button.classList.add('copied');
        }
      });
    });

    const checklistKey = 'easycat-recording-checklist-v1';
    const checks = [...document.querySelectorAll('[data-check]')];
    const saved = JSON.parse(localStorage.getItem(checklistKey) || '{}');
    checks.forEach((input) => {
      input.checked = Boolean(saved[input.dataset.check]);
      input.addEventListener('change', () => {
        const state = {};
        checks.forEach((item) => { state[item.dataset.check] = item.checked; });
        localStorage.setItem(checklistKey, JSON.stringify(state));
      });
    });
    document.getElementById('reset-checklist').addEventListener('click', () => {
      checks.forEach((input) => { input.checked = false; });
      localStorage.removeItem(checklistKey);
    });
  </script>
</body>
</html>
