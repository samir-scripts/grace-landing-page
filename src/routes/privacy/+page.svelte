<script lang="ts">
  import { base } from '$app/paths';
  import { ArrowLeft } from '@lucide/svelte';
</script>

<svelte:head>
  <title>privacy policy · grace.</title>
  <meta name="description" content="Privacy policy for the grace. Focus Companion for Google Chrome." />
</svelte:head>

<div class="policy">
  <nav aria-label="Privacy navigation">
    <a class="back-link" href={`${base}/`}><ArrowLeft aria-hidden="true" />back to grace.</a>
  </nav>
  <header>
    <p class="policy-name">grace. Focus Companion · Chrome</p>
    <h1>privacy policy</h1>
  </header>
  <article aria-label="grace. Focus Companion Privacy Policy">
    <p><strong>Effective date:</strong> November 11, 2026.</p>
    <p><strong>Applies to:</strong> grace. Focus Companion for Google Chrome desktop, version 1.1.0 and later. The extension requires Chrome 116 or later. This policy is about the Chrome companion; Grace desktop has its own local application behavior.</p>
    <h2>Summary</h2>
    <p>The extension connects Google Chrome on your computer to the Grace desktop app on that same computer. It uses the connection only to pair a browser profile and apply or release website blocking rules chosen in Grace. It has no account system, remote service, advertising, analytics, or telemetry. It does not send browsing history or visited page URLs to Grace&#x27;s publisher or another remote service.</p>
    <h2>Data the extension handles</h2>
    <p>The extension stores the following items in Chrome&#x27;s extension-local storage on your device:</p>
    <ul>
    <li>Your consent decision.</li>
    <li>A pairing token, the configured local connection port, and a persistent profile identifier. Automatic pairing can create and store these values after consent. They are credentials and should be treated as sensitive.</li>
    <li>A cached set of limited appearance colors and animation preference so the options page can match Grace&#x27;s theme.</li>
    </ul>
    <p>Chrome stores browser blocking rules installed through its Declarative Net Request API separately from extension-local storage.</p>
    <p>After you select <strong>Accept &amp; connect</strong>, the extension sends a persistent profile identifier and authentication proofs to Grace over a local connection to <code>127.0.0.1</code> on the configured port. It sends policy acknowledgements and recurring connection checks over that authenticated local connection. Grace sends the blocking policy, including the domains you configured in Grace, to the extension over the same connection. The extension applies those rules in Chrome. It may inspect open tab URLs locally to replace a tab matching an active blocked domain with its bundled blocked page; the blocked-page URL includes the original tab URL, which may remain in the browser tab or history. These URLs are not sent to the publisher or another remote service. The blocked page displays the matched hostname locally.</p>
    <p>Before consent, the options page may request Grace&#x27;s local appearance settings so it can display a matching theme. That request does not pair the browser or send browsing data. The extension does not collect browser identity, device details, visited URLs, or error reports for the publisher.</p>
    <h2>Permissions</h2>
    <ul>
    <li><strong>Storage:</strong> save consent, local pairing settings, profile identifier, and cached appearance.</li>
    <li><strong>Alarms:</strong> retry the local connection periodically.</li>
    <li><strong>Declarative Net Request:</strong> add, replace, or remove locally enforced rules for configured blocked domains.</li>
    <li><strong>Tabs:</strong> inspect open tabs locally and redirect tabs matching an active blocking policy to the extension&#x27;s blocked page.</li>
    <li><strong>Native messaging:</strong> communicate with the Grace desktop app installed on the same computer for automatic pairing and appearance settings.</li>
    <li><strong>HTTP and HTTPS site access:</strong> apply configured blocking rules to sites across <code>http://</code> and <code>https://</code> origins. This broad access is required because the user may choose any domain to block; the extension does not read or upload page contents.</li>
    </ul>
    <h2>Consent, withdrawal, and retention</h2>
    <p>The extension remains disconnected until you accept the disclosure in its options page. If you decline, it saves that choice locally and does not pair or exchange focus-session data. Consent is saved in Chrome extension storage.</p>
    <p>Select <strong>Stop sharing and disconnect</strong> to close the connection and stop future sharing. Withdrawal does not necessarily remove rules already installed in Chrome: they can remain until Grace later sends an authenticated release or replacement policy. If Grace is unavailable, keep in mind that the browser may retain those rules. The consent decision, pairing values, profile identifier, and cached appearance remain in local extension storage after withdrawal; remove the extension or clear its data in Chrome to remove the extension&#x27;s stored data. Removing the extension also removes its Chrome blocking rules.</p>
    <p>The extension publisher receives and retains no data. Grace stores its own focus profiles and settings locally on your computer; manage or delete those in Grace. Chrome controls its own extension storage lifecycle and backup behavior.</p>
    <h2>Sharing and security</h2>
    <p>Data is exchanged only with the Grace app&#x27;s local service on your computer. The extension does not contact a publisher server or share data with third parties. The pairing token authenticates the local connection; do not share it. Anyone with access to your computer or Chrome profile may be able to access local extension data.</p>
    <h2>Changes and contact</h2>
    <p>This policy will be updated if the extension&#x27;s data handling changes. For support or privacy questions, open an issue at <a href="https://github.com/samir-scripts/grace/issues">github.com/samir-scripts/grace/issues</a>. Do not include pairing tokens or other sensitive information in an issue.</p>
  </article>
</div>

<style>
  .policy { width: 100%; max-width: 640px; min-width: 0; }
  nav { border-bottom: 1px solid var(--color-rule); }
  .back-link { display: inline-flex; align-items: center; gap: 12px; min-height: 44px; padding: 10px 12px; font-size: 14px; line-height: 1.5; text-decoration: none; }
  .back-link :global(svg) { width: 16px; height: 16px; flex: 0 0 auto; }
  .back-link:hover, .back-link:focus-visible { background: var(--color-row-hover); }
  header { padding: 24px 12px 16px; }
  .policy-name { margin: 0 0 8px; color: var(--color-muted); font-size: 12px; line-height: 1.5; }
  h1 { margin: 0; font-size: 20px; line-height: 1.5; font-weight: 400; }
  article { padding: 0 12px 24px; font-size: 13px; line-height: 1.8; overflow-wrap: anywhere; }
  h2 { margin: 24px 0 8px; color: var(--color-muted); font-size: 12px; line-height: 1.5; font-weight: 400; text-transform: lowercase; }
  p { margin: 0 0 16px; }
  article > :last-child { margin-bottom: 0; }
  ul { margin: 0 0 16px; padding-left: 20px; list-style: disc; }
  li { padding-left: 4px; margin: 0 0 8px; }
  strong { font-weight: 600; }
  code { font: inherit; }
  a { color: inherit; text-decoration: underline; text-underline-offset: 3px; }
  a:hover { color: var(--color-wave-coral); }
  a:focus-visible { outline: 2px solid var(--color-foreground); outline-offset: 4px; }
</style>
