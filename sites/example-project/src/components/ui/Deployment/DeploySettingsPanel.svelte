<script>
    export let settings
    import NetlifyDeploy from "./NetlifyDeploy.svelte";
    import VercelDeploy from "./VercelDeploy.svelte";
    import OtherDeploy from "./OtherDeploy.svelte";
    import { slide, blur } from 'svelte/transition'

    let deploymentOptions = [
        {name: 'Choose a deployment target'},
		{id: 'netlify', name: 'Netlify', formComponent: NetlifyDeploy},
        {id: 'vercel', name: 'Vercel', formComponent: VercelDeploy},
		{id: 'other', name: 'Self-host (other)', formComponent: OtherDeploy}
	];

    let selectedDeployment = deploymentOptions[0]

</script>

<form>
<div class=container>
    <div class=panel> 
    <h1>Deployment</h1>
    <p>Evidence projects can be deployed to a variety of cloud environments. If you haven't done this type of thing before, we would suggest using Netlify.</p>
    <h2>Deployment Environment</h2>
    <select bind:value={selectedDeployment}>
        {#each deploymentOptions as option}
        <option value={option}>
            {option.name}
        </option>
        {/each}
    </select>
    </div>
    {#if selectedDeployment.formComponent}
    <div class=panel transition:slide|local>
        <svelte:component this={selectedDeployment.formComponent} {settings}/>
    </div>
    {/if}
</div>
<footer>
    <span>Learn more about <a class=docs-link href="https://docs.evidence.dev/deployment/deployment-overview">Deploying your Project &rarr;</a></span>

</footer>
</form>
<style>
    h2 {
        text-transform: uppercase;
        font-weight: normal;
        font-size: 14px;
    }
    select {
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;   
        padding:0.75em;
        width: 100%;
        border: 1px solid var(--grey-200); 
        font-family: var(--ui-font-family);
        color: var(--grey-800); 
        margin: 0.5em 0 0 0; 
        transition: all 400ms;
        cursor: pointer;
    }

        select:hover{
            border: 1px solid var(--grey-300);
            transition: all 400ms;
            box-shadow: 0 5px 5px 2px hsl(0deg 0% 97%);
    }

        select:focus {
            outline: none;

    }

    .container {
        margin-top: 2em;
        border-top: 1px solid var(--grey-200);
        border-left: 1px solid var(--grey-200);
        border-right: 1px solid var(--grey-200);
        border-radius: 5px 5px 0 0;
        font-size: 14px; 
        font-family: var(--ui-font-family);
    }   

    .panel {
        border-top: 1px solid var(--grey-200);
        padding:1.0em;
    }

    .panel:first-of-type {
        border-top:none;
    }

    footer {
        border: 1px solid var(--grey-200);
        border-radius: 0 0 5px 5px;
        background-color: var(--grey-100);
        padding:1.0em;
        display:flex;
        font-size: 14px;
        align-items: center;
        font-family: var(--ui-font-family);
    }

    .docs-link {
        color: var(--blue-600);
        text-decoration: none;
    }

    .docs-link:hover {
        color: var(--blue-800);
    }

</style>