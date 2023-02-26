<script>
	import TableHeader from './TableHeader.svelte';
	import TableBody from './TableBody.svelte';
	import AddNewInput from './AddNewInput.svelte';
	import ExportButton from './ExportButton.svelte';
	
	import {onMount} from 'svelte';

	let languages = [];
	let keys = [];
	let translations_matrix = {};

	onMount(async () => {
        let response = await fetch(
            'http://localhost:38115/languages',
            {
                method: 'GET',
            }
        );
        languages = await response.json();

		response = await fetch(
            'http://localhost:38115/localization/keys',
            {
                method: 'GET',
            }
        );
        keys = await response.json();

		response = await fetch(
			'http://localhost:38115/localization/translations',
			{
				method: 'GET',
			}
		);
		let translations = await response.json();
		for(let i=0; i<languages.length; i++){
			for(let j=0; j<keys.length; j++){
				if(!translations_matrix[languages[i].id]){
					translations_matrix[languages[i].id] = {};
				}

				let found = false;
				for(let k=0; k<translations.length; k++){
					if(translations[k].localization_key_id == keys[j].id && translations[k].language_id == languages[i].id){
						translations[k].updated_translation = translations[k].translation;
						translations_matrix[translations[k].language_id][translations[k].localization_key_id] = translations[k];

						found = true;
						break;
					}
				}
				if(!found){
					translations_matrix[languages[i].id][keys[j].id] = {
						language_id: languages[i].id,
						localization_key_id: keys[j].id,
						translation: '',
						updated_translation: ''
					};
				}
			}
		}
    });

	async function save() {
		
		let data = {
			translations: []
		};

		for (var key in translations_matrix) {
			if (!translations_matrix.hasOwnProperty(key)) continue;

			var obj = translations_matrix[key];
			for (var prop in obj) {
				if (!obj.hasOwnProperty(prop)) continue;

				if(obj[prop].translation != obj[prop].updated_translation){
					obj[prop].translation = obj[prop].updated_translation;
					data.translations.push(obj[prop]);
				}
			}
		}

        let response = await fetch(
            'http://localhost:38115/localization/translations',
            {
                method: 'PUT',
                headers: {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            }
        );

        await response.json();
	}
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<h1>
		Localization app
	</h1>

	<div>
		<AddNewInput parameter_name="key"></AddNewInput>
	</div>

	<div>
		<AddNewInput parameter_name="language"></AddNewInput>
	</div>

	<br><br>
		<ExportButton type="csv"></ExportButton>
		<ExportButton type="json"></ExportButton>
	<br>

	<table>
		<TableHeader languages={languages}></TableHeader>
		<TableBody languages={languages} keys={keys} translations={translations_matrix}></TableBody>
	</table>

	<input type="submit" value="Save" on:click={save}>


</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		display: block;
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
