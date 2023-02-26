<script>

	export let type;

	async function export_translations() {
		
        let payload = {type: type};

        fetch(
            'http://localhost:38115/localization/export',
            {
                method: 'POST',
                body: JSON.stringify(payload),
                headers: {
                    'Content-Type': 'application/json'
                },
            }
        ).then( res => res.blob() )
        .then( blob => {
            var file = window.URL.createObjectURL(blob);
            window.location.assign(file);
        });
	}
</script>

<input type="submit" value="Export {type}" on:click={export_translations}>

<style>
</style>
