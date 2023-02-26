<script>
	export let languages;
</script>

<tr>
    <th>Key</th>
    {#each languages as { id, name }, i}
		<th>{name}</th>
	{/each}
</tr>

<style>
</style>
