<script>
  import {onMount} from 'svelte';
  let {onDisabled=()=>{},contained=false,showDisabled=false}=$props();
  let state=$state(null),error=$state(''),busy=$state(false);
  async function request(action){
    busy=true;error='';
    try{
      const response=await fetch('/@_connect',action?{method:'POST',headers:{'content-type':'application/json'},body:JSON.stringify({action})}:{cache:'no-store'});
      const value=await response.json();if(!response.ok)throw new Error();
      state=value;if(value.enabled===false)onDisabled();
    }catch{error='The account connection could not be checked. Please try again.';}
    finally{busy=false;}
  }
  onMount(()=>{let active=true;request().then(()=>{if(active&&state?.status==='connected'&&!error)request('refresh');});const timer=setInterval(()=>{if(state?.status==='awaiting_confirmation'&&!busy&&!error)request('poll');},5000);return()=>{active=false;clearInterval(timer);};});
</script>

{#if state?.enabled || error || (showDisabled && state)}
  <section class="account-link" class:contained aria-labelledby="connect-account-title" aria-busy={busy}>
    <div class="heading"><span class="mark" aria-hidden="true">↔</span><div><small>EXTAAS CONNECT</small><h2 id="connect-account-title">{state?.status==='connected'?'Your connected account':'Connect your account'}</h2></div></div>
    {#if showDisabled && state?.enabled===false}<p role="status">Account linking is not enabled by this Core deployment. Ask its administrator to configure the account service, then try again.</p>{/if}
    {#if error}<p class="error" role="alert">{error}</p>{/if}
    {#if state?.status==='connected'}
      <dl><div><dt>Account</dt><dd>{state.account_email}</dd></div><div><dt>Connection</dt><dd>Linked to this Workspace Core</dd></div></dl>
      <p>Manage your services and this connection in OUX Iseteenindus. Paid access is checked separately.</p>
      <div class="actions"><a class="primary" href={state.manage_url} target="_blank" rel="noopener noreferrer">Open Iseteenindus ↗</a><button disabled={busy} onclick={()=>request('refresh')}>Check connection</button></div>
    {:else if state?.status==='awaiting_confirmation'}
      <p>Open Iseteenindus, sign in, and check that this code matches before confirming the connection.</p>
      <div class="code"><small>CONFIRMATION CODE</small><strong>{state.user_code}</strong></div>
      <div class="actions"><a class="primary" href={state.verification_uri} target="_blank" rel="noopener noreferrer">Continue in Iseteenindus ↗</a><button disabled={busy} onclick={()=>request('poll')}>Check confirmation</button></div>
      <p class="note">This request expires after ten minutes. Keep this page open while you confirm.</p>
    {:else}
      <p>Link this installation to your OUX account and manage its services in Iseteenindus.</p>
      <button class="primary" disabled={busy} onclick={()=>request(state?.enabled?'begin':undefined)}>{busy?'Connecting…':state?.enabled?'Connect account':'Try again'}</button>
    {/if}
    {#if state?.reason}<p class="note" role="status">{state.reason}</p>{/if}
  </section>
{/if}
<style>
  .account-link{padding:24px;border:1px solid #d0d5dd;border-radius:10px;background:#fff;color:#172033;min-width:0}.heading{display:flex;align-items:center;gap:14px}.mark{display:grid;place-items:center;flex:none;width:44px;height:44px;border:1px solid #dfd3b0;border-radius:8px;background:#fbf8ee;color:#6b5010;font-size:22px}.heading small,.code small{font-size:10px;letter-spacing:.12em;color:#665019}h2{font:600 21px/1.3 system-ui;margin:6px 0 0}p{font:14px/1.7 system-ui;color:#475467;margin:20px 0}.actions{display:flex;align-items:center;flex-wrap:wrap;gap:12px}button,.actions a{box-sizing:border-box;display:inline-flex;align-items:center;justify-content:center;min-height:44px;padding:11px 16px;border:1px solid #b8c0cd;border-radius:6px;background:#fff;color:#172033;font:600 13px/1.5 system-ui;text-decoration:none;cursor:pointer}.primary{background:#172033!important;color:#fff!important;border-color:#172033!important}button:disabled{opacity:.65;cursor:wait}.code{display:grid;gap:10px;margin:22px 0;padding:18px;background:#f7f8fa;border:1px solid #d0d5dd;border-radius:6px}.code strong{font:600 23px/1.3 ui-monospace,monospace;letter-spacing:.12em;color:#172033}.note{font-size:12px;margin-bottom:0}.error{padding:14px;border:1px solid #e7b9b9;border-radius:6px;background:#fff7f7;color:#8a2525}dl{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:18px;padding:20px 0;margin:20px 0 0;border-block:1px solid #e4e7ec}dt{font:12px/1.5 system-ui;color:#475467}dd{margin:6px 0 0;font:14px/1.5 system-ui;overflow-wrap:anywhere}a:focus-visible,button:focus-visible{outline:3px solid #8b650d;outline-offset:3px}@media(max-width:600px){.account-link{padding:18px}dl{grid-template-columns:1fr}.actions>*{width:100%}}
.account-link.contained{padding:0;border:0;border-radius:0}
</style>
