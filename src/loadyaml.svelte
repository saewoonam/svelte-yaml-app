<script>
  let name = 'world';
  export let files;
  // export let data;
  //https://blog.mounirmesselmeni.de/2012/11/20/reading-csv-file-with-javascript-and-html5-file-api/
  $: if (files) {
    handleFiles(files);
  }
  function handleFiles(files) {
      // Check for the various File API support.
      if (window.FileReader) {
          console.log("file:", files[0])
          getAsText(files[0]);
          // FileReader are supported.
      } else {
          alert('FileReader are not supported in this browser.');
      }
  }
  function getAsText(fileToRead) {
    var reader = new FileReader();
    // Read file into memory as UTF-8      
    reader.readAsText(fileToRead);
    // Handle errors load
    reader.onload = loadHandler;
    reader.onerror = errorHandler;
  }
  function loadHandler(event) {
    var csv = event.target.result;
    console.log(csv);
  }

  function processData(csv) {
    var allTextLines = csv.split(/\r\n|\n/);
    var lines = [];
    for (var i=0; i<allTextLines.length; i++) {
      if (!allTextLines[i].startsWith('#')) {
        var data = allTextLines[i].split(',');
        var tarr = [];
        for (var j=0; j<data.length; j++) {
          tarr.push(parseFloat(data[j]));
        }
        lines.push(tarr);
      }
    }
    let m = lines
    data = m[0].map((x,i) => m.map(x => x[i]))
    console.log('data', data);
  }

  function errorHandler(evt) {
    if(evt.target.error.name == "NotReadableError") {
      alert("Cannot read file !");
    }
  }

</script>

<input type="file" id="csvFileInput" bind:files
       accept=".csv">

{#if files}
  <h2>Selected files:</h2>
  {#each Array.from(files) as file}
    <p>{file.name} ({file.size} bytes) </p>
  {/each}
{/if}

