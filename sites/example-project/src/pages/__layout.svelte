<!-- This get's shipped with the template -- don't do local imports from $lib -->

<script context="module">
	// Build nav links
	const rootMDFiles = import.meta.glob('/src/pages/*.md');
	const levelOneIndexFiles = import.meta.glob('/src/pages/*/index.md');
	const levelOneMDFiles = import.meta.glob('/src/pages/*/*.md');
	const levelTwoIndexFiles = import.meta.glob('/src/pages/*/*/index.md');

	let menu = [];
	let pathEnd;
	let pathSplit;

	for(let path in rootMDFiles) {
		pathEnd = path.replace('/src/pages/', '').replace(/^\.\//, '')
		if (path.includes('[')) continue;
		menu.push({
			filename: pathEnd,
			label: pathEnd.replace(/\.md$/, '').replace(/_/g, ' ').replace(/-/g, ' '),
			href: "/" + pathEnd.replace(/^index\.md/, '').replace(/\.md$/, ''),
			hrefUri: encodeURI("/" + pathEnd.replace(/^index\.md/, '').replace(/\.md$/, '')),
			folder: undefined,
			folderLabel: undefined,
			folderHref: undefined,
			folderHrefUri: undefined,
			nameError: pathEnd.includes(" "),
			folderNameError: undefined
		})
	}

	for(let path in levelOneIndexFiles) {
		pathEnd = path.replace('/src/pages/', '').replace(/^\.\//, '')
		pathSplit = pathEnd.split("/")
		menu.push({
			filename: pathSplit[1],
			label: pathSplit[0].replace(/_/g, ' ').replace(/-/g, ' '),
			href: "/" + pathSplit[0],
			hrefUri: encodeURI("/" + pathSplit[0]),
			folder: pathSplit[0],
			folderLabel: pathSplit[0].replace(/_/g, ' ').replace(/-/g, ' '),
			folderHref: "/" + pathSplit[0],
			folderHrefUri: encodeURI("/" + pathSplit[0]),
			nameError: pathEnd.includes(" "),
			folderNameError: pathSplit[0].includes(" ")
		})
	}

	for(let path in levelOneMDFiles) {
		pathEnd = path.replace('/src/pages/', '').replace(/^\.\//, '')
		pathSplit = pathEnd.split("/")
		if(!path.includes("/index.md") && !path.includes("[")){
			menu.push({
				filename: pathSplit[1],
				label: pathSplit[1].replace(/\.md$/, '').replace(/_/g, ' ').replace(/-/g, ' ').replace(/.*\//,''),
				href: "/" + pathEnd.replace(/\.md$/, ''),
				hrefUri: encodeURI("/" + pathEnd.replace(/\.md$/, '')),
				folder: pathSplit[0],
				folderLabel: pathSplit[0].replace(/_/g, ' ').replace(/-/g, ' '),
				folderHref: "/" + pathEnd.replace(/\.md$/, '').replace(/^\.\//, '').replace(/\/([^\/]+)$/, ''),
				folderHrefUri: encodeURI("/" + pathSplit[0]),
				nameError: pathEnd.includes(" "),
				folderNameError: pathSplit[0].includes(" ")
			})
		}
	}

	for(let path in levelTwoIndexFiles) {
		pathEnd = path.replace('/src/pages/', '').replace(/^\.\//, '')
		pathSplit = pathEnd.split("/")
		menu.push({
			filename: pathSplit[2],
			label: pathSplit[1].replace(/_/g, ' ').replace(/-/g, ' '),
			href: "/" + pathSplit[0] + "/" + pathSplit[1],
			hrefUri: encodeURI("/" + pathSplit[0] + "/" + pathSplit[1]),
			folder: pathSplit[0],
			folderLabel: pathSplit[0].replace(/_/g, ' ').replace(/-/g, ' '),
			folderHref: "/" + pathSplit[0],
			folderHrefUri: encodeURI("/" + pathSplit[0]),
			nameError: pathEnd.includes(" "),
			folderNameError: pathSplit[0].includes(" ")
		})
	}

	let folders = [...new Set(menu.map(item => item.folder))];
	folders = folders.filter(d => d !== undefined);

	let fileCount;
	let folderList = [];
	let folderObj;
	let folderLink;
	let contents;

	let folderLab;
	let folderHref;
	let folderHrefUri;
	let indexFileCount;
	let folderNameError;

	for(let i = 0; i < folders.length; i++){
		contents = menu.filter(d => d.folder === folders[i]);

		folderLab = contents[0].folderLabel;
		folderHref = contents[0].folderHref;
		folderHrefUri = contents[0].folderHrefUri;
		folderNameError = contents[0].folderNameError;

		fileCount = contents.filter(d => d.href !== folderHref).length;
		indexFileCount = contents.filter(d => d.href === folderHref).length;
		folderLink = contents.filter(d => d.href === folderHref).length > 0;
		folderObj = {folder: folders[i], folderLabel: folderLab, folderHref: folderHref, folderHrefUri: folderHrefUri, fileCount: fileCount, indexFileCount: indexFileCount, folderLink: folderLink, folderNameError: folderNameError}
		folderList.push(folderObj)
	}

</script>

<script>
	import "../app.css"
	import { navigating } from '$app/stores';
	import { blur } from "svelte/transition";
	import { page } from "$app/stores";
	import {dev} from '$app/env'

	import TableOfContents from "@evidence-dev/components/TableOfContents.svelte";
	import Header from '@evidence-dev/components/ui/Header.svelte'
	import Hamburger from '@evidence-dev/components/ui/Hamburger.svelte'
	import Sidebar from '@evidence-dev/components/ui/Sidebar.svelte'
	import LoadingIndicator from "@evidence-dev/components/ui/LoadingIndicator.svelte";
	
	import QueryStatus from "@evidence-dev/components/QueryStatus.svelte";
	
	export let open = false  
</script>

<svelte:head>
	<title>Evidence</title>
</svelte:head>

{#if $navigating}
	<LoadingIndicator/>
{/if}

<div class="grid">
	{#if $page.path !== '/settings'}
		<div class="header-bar">
			<Header {menu} {folderList}/>
			<Hamburger bind:open/>
		</div>
	{/if}
	<Sidebar bind:open {menu} {folderList}/>
	<main in:blur|local>
	  <div class=content class:settings-content={$page.path === '/settings'}>
		<article class:settings-article={$page.path === '/settings'}>
			<slot/>
			<p>&nbsp;</p>
		</article>
		<aside class='toc'>
			<TableOfContents/>
		</aside>
	  </div>
	</main>
</div>
{#if !$navigating && dev}
<QueryStatus /> 
{/if}

<style>
.grid {
  display: grid;
  grid-template-areas:
    'sidebar header'
    'sidebar main';
  grid-template-columns: 18rem 5fr;
  grid-template-rows: var(--header-height) 1fr;
  gap: 0 16px;
  margin: 0 auto;
  isolation: isolate;
}

main {
  grid-area: main;
}

div.content {
	margin: auto;
	max-width:100ch;
	box-sizing: border-box;
	display: grid;
	grid-template-columns: 4fr minmax(0,1fr);
	gap: 0 5ch;
  	grid-template-areas:
    	'article toc';
    justify-items: left;
}

article {
	max-width: 70ch;
	min-width: 0;
	width: 100%;
	grid-area: article;
	padding: 0 1.5em 0 1.5em;
	box-sizing: border-box;
	user-select: text;
	-moz-user-select: text;
	-webkit-user-select: text;
	-ms-user-select: text;
}

.settings-content {
	max-width: 100ch !important;
	grid-template-columns: 1fr !important;
	grid-template-areas:
    	'article' !important;
}

.settings-article {
	max-width: 100ch;
	min-width: 0;
	width: 100%;
	grid-area: article;
	padding: 0 1.5em 0 1.5em;
	box-sizing: border-box;
}
aside.toc {
	grid-area: toc;
	padding: 0px;
}

.header-bar {
	display: flex;
	justify-content: space-between;
    position: sticky;
    z-index: 2;
    top: 0;
    background-color: rgba(255, 255, 255, 0.73);
    -webkit-backdrop-filter: blur(10px) saturate(1.8);
    backdrop-filter: blur(10px) saturate(1.8);
}
@media (max-width: 1440px) {
	div.content {
		grid-template-columns: 1fr;
		grid-template-areas:
			'article';
	}

	article {
		max-width: 70ch;
	}

	.settings-article {
		max-width: 100ch;
	}

	.settings-content {
	max-width: 100ch !important;
	grid-template-columns: 1fr !important;
	grid-template-areas:
    	'article' !important;
}

	aside.toc {
		display: none;
	}
}

@media (max-width: 850px) {
	.grid {
		display: grid;
		grid-template-areas:
			'header'
			'main';
		grid-template-columns: 1fr;
		grid-template-rows: var(--header-height) 1fr;
		margin: 0 auto;
		isolation: isolate;
	}
}

@media print {
  main * {
    visibility: hidden;
  }
  article, article * {
    visibility: visible;
  }
  article {
    position: absolute;
    left: 0;
    top: 0;
  }
}

</style>
