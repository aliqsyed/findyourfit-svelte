<script>
	import { onMount } from 'svelte';
	import FindYourFitForm from './components/FindYourFitForm.svelte'
	import Campus from './components/Campus.svelte'

	let facilities = {}
  let campuses = {}
	const dataurl = "http://gisd.test/api/find-your-fit---campus-data"

	onMount(async () => {
		const tempFacilities = {};
		const response = await fetch(dataurl)
		const data = await response.json()
	
		data.forEach(function(facility) {
			tempFacilities[facility.nid] = facility;
		});
		
		facilities = tempFacilities;
		//console.log(facilities)
	});

	function processProgramsData(event) {
			let programs = event.detail.data;
      const campus = {};

      programs.forEach(program => {
        let locations =
          program.location.indexOf(",") > -1
            ? program.location.split(",")
            : [program.location];

        locations.forEach(locationId => {
          if (!campus[locationId]) {
            campus[locationId] = {};
            campus[locationId]["programs"] = [];
            campus[locationId]["ptech"] = [];
            campus[locationId]["ptechpathway"] = [];
          }
          if (program.program_id == "10821") {
            campus[locationId]["ptech"].push({
              title: program.title,
              is_magnet: program.magnet === "Yes",
              program_id: program.program_id,
              webpage: program.webpage
            });
          } else if (
            program.other_categorization &&
            program.other_categorization
              .toLowerCase()
              .includes("p-tech pathway")
          ) {
            campus[locationId]["ptechpathway"].push({
              title: program.title,
              is_magnet: program.magnet === "Yes",
              program_id: program.program_id,
              webpage: program.webpage
            });
          } else {
            campus[locationId]["programs"].push({
              title: program.title,
              is_magnet: program.magnet === "Yes",
              program_id: program.program_id,
              webpage: program.webpage
            });
          }

          campus[locationId]["campusinfo"] = this.facilities[locationId];
        });
      });
      campuses = campus;
			console.log("In process programs data");
  }

</script>

<div>
<FindYourFitForm on:processdata={processProgramsData} on:clearresults={campuses = {}} />
{#each campuses as campus, key}
	<Campus campus={campus} />
{/each}

</div>

<style>

</style>