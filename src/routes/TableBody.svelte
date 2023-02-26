<script>
	export let languages;
    export let keys;
    export let translations;
</script>


    {#each keys as key}
        <tr>
            <td>{key.key}</td>
            {#each languages as language}
                <td>
                    {#if translations && translations[language.id] && translations[language.id][key.id]}
                        <input bind:value={translations[language.id][key.id].updated_translation}>
                    {/if}
                </td>
            {/each}

        </tr>
	{/each}

<style>
</style>
