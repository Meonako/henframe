<script lang="ts">
    import type { PageData } from './$types';
    import Card from '$lib/components/card.svelte';
    import { page } from '$app/stores';

    export let data: PageData;

    $: ({ result } = data);

    $: term = $page.params.term;
    $: currentPage = parseInt($page.params.page);

    let lastPage: number;
    $: if (result.success) {
        if (result.total == 0) {
            lastPage = currentPage + 1;
        } else {
            let totalPage = result.total / 25;
            lastPage = Number.isInteger(totalPage) ? totalPage : Math.floor(totalPage) + 1;
        }
    } else {
        lastPage = currentPage;
    }

    $: previousPage = currentPage == 1 ? 1 : currentPage - 1;
    $: nextPage = currentPage == lastPage ? currentPage : currentPage + 1;
</script>

<svelte:head>
    {#if result.success && term}
        {#key term}
            <title>{term.toUpperCase()} | Search</title>
        {/key}
    {/if}
</svelte:head>

{#if result.success}
    <h1>Found <span>{result.total}</span> items</h1>
    <h3>Total page: <span>{lastPage}</span></h3>
    <div class="container">
        {#each result.data as res}
            <Card
                id={res.id}
                title={res.title.display}
                width={res.images.cover.info.width}
                height={res.images.cover.info.height}
                link={res.images.cover.link}
            />
        {/each}
    </div>
    <div class="container btn-container">
        {#if currentPage != 1}
            <a href="/search/{term}/1">1</a>
        {/if}

        {#if previousPage > 2}
            <div class="dot">&nbsp; - - - &nbsp;</div>
        {/if}

        {#if previousPage != 1}
            <a href="/search/{term}/{previousPage}">{previousPage}</a>
        {/if}

        <!-- svelte-ignore a11y-missing-attribute -->
        <a class="active">{currentPage}</a>

        {#if lastPage != 1}
            {#if lastPage - currentPage >= 1}
                <a href="/search/{term}/{nextPage}">{nextPage}</a>
            {/if}

            {#if lastPage - nextPage > 1}
                <div class="dot">&nbsp; - - - &nbsp;</div>
            {/if}

            {#if lastPage - nextPage >= 1}
                <a href="/search/{term}/{lastPage}">{lastPage}</a>
            {/if}
        {/if}
    </div>
{:else}
    <h1>Nothing Found!</h1>
{/if}

<style>
    .container {
        width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
        margin-bottom: 1rem;
    }

    .btn-container {
        margin-bottom: 2rem;
    }

    a {
        padding: 1rem;
        border: 1px solid red;
        transition: 0.3s;
    }

    @media (min-width: 768px) {
        a:hover {
            background: red;
            color: white;
            text-decoration: none;
        }
    }

    @media (max-width: 768px) {
        a:active {
            background: red;
            color: white;
            text-decoration: none;
        }
    }

    .active {
        background: red;
        color: white;
        text-decoration: none;
    }

    .dot {
        display: flex;
        align-items: center;
    }

    span {
        color: yellowgreen;
    }

    h3 {
        text-align: center;
    }
</style>
