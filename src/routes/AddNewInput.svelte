<script>

	export let parameter_name;
    let parameter_value = "";

	async function save() {
		if(!parameter_value){
            return false;
        }

        let payload = {},
            endpoint = "";

        switch(parameter_name) {
            case "key":
                payload.key = parameter_value;
                endpoint = "/localization/keys";
                break;
            case "language":
                payload.name = parameter_value;
                endpoint = "/languages";
                break;
            default:
                return false;
        }

        let response = await fetch(
            'http://localhost:38115' + endpoint,
            {
                method: 'POST',
                headers: {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(payload)
            }
        );

        await response.json();
        location.reload();
	}
</script>

<span>Add new {parameter_name}: </span>
<input type="text" bind:value={parameter_value}>
<input type="submit" value="Submit" on:click={save}>



<style>
</style>
