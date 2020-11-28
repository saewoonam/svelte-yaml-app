<script>
	let files;
	let settings;
	// import Select from './loadyaml.svelte'
	import yaml from 'js-yaml'
	import JSONEditor from 'jsoneditor';
	let container;
	let width = 400;
	let height = 300;
  let editor;
	// console.log(yaml)
	// console.log(yaml.safeDump({a:1, b:2, c:3}))
	var reader;
	$: if (files) {
    console.log(files[0])
    reader = new FileReader();
    // Read file into memory as UTF-8      
    reader.readAsText(files[0]);
    // Handle errors load
    reader.onload = loadHandler;
    reader.onerror = errorHandler;		
  }
	$: if (settings) { 
		console.log('settings', settings)
	}
	function loadHandler(event) {
		var txt = event.target.result;
		console.log(txt);
		settings = yaml.safeLoad(txt);
		const options = {
			mode: 'tree', // 'view' disable edit
			onChange: function () {
				console.log('onChange')
				let updated = editor.get()
				settings = updated;
				// changed = true;
			}
		}
		editor = new JSONEditor(container, options)
		// set json
		editor.set(settings)
	}
  function errorHandler(evt) {
		console.log(evt)
    if(evt.target.error.name == "NotReadableError") {
      alert("Cannot read file !");
    }
  }
	let blob;
	function save() {
		// console.log(settings);
		editor.set(settings)
		const content = yaml.dump(settings)
		blob = new Blob([content], { type: 'text/yaml' });
		// console.log(blob)
		if (
      window.navigator && 
      window.navigator.msSaveOrOpenBlob
    ) { 
			console.log('navigator');
			window.navigator.msSaveOrOpenBlob(blob);
		} else {
			console.log('create link a')
			let a = document.createElement('a')
			a.href = window.URL.createObjectURL(blob);
			a.download = 'download.yaml'
			// a.download = ''
			// a.innerHTML = 'right clock to save'
			console.log(a)
			// document.body.appendChild(a);
			//a.click();
			a.dispatchEvent(
				new MouseEvent('click', { 
					bubbles: true, 
					cancelable: true, 
					view: window 
				})
			// a.remove()
  );
		}
	}
</script>
<link href="https://cdnjs.cloudflare.com/ajax/libs/jsoneditor/9.1.2/jsoneditor.css" rel="stylesheet" type="text/css"> 
{#if !settings}
<input type="file" bind:files accept="*.yml">
{/if}
<div bind:this={container} style="width: {width}px; height: {height}px;"></div>
{#if settings}
<button on:click={save}>
	save
</button>

{/if}
