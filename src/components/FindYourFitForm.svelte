<script>
	import { createEventDispatcher } from 'svelte';
	import { onMount } from 'svelte';

	const dispatch = createEventDispatcher();
	const dataurl = "http://gisd.test/api/find-your-fit"

	let programs = []
	let interests = []
	let interestsSelected = []
	let level = ""
  let showInterestsForm = true

  onMount(async () => {
    const response = await fetch(dataurl)
    const data = await response.json()
    programs = data  
	});

  function getIcon(key) {
    const iconPath = '/images/'
    const icons = {
      'Accelerated/Advanced Curriculum' : 'ac',
      'Languages Other Than English' : 'lote',//change
      'Military' : 'military',
      'STEM (Science, Technology, Engineering, Math)' : 'stem',
      'Visual & Performing Arts (Art, Music, Theater)' : 'vapa',
      'Business' : 'business',
      'Law & Criminal Justice' : 'law',
      'Radio, TV, Film': 'radio',
      'Teaching' : 'teaching',
      'Montessori' : 'montessori'//change 
    }

  const iconExtension = 'png'

  if (key in icons) {
    return `${iconPath}${icons[key]}.${iconExtension}`
  }

  return ""
}

  function showInterestsWithCampus() {
      showInterestsForm = false;
      dispatch('changecampuslistvisibility', {'show': true})
  }

	function getInterestsForLevel() {
		interestsSelected = []
    showInterestsForm = true
		interests = [
			...new Set(
				programs
					.filter(program => (program.level === level ? true : false))
					.map(program => program.interests)
					.join("|")
					.split("|")
					.sort()
			)
		];
    
    dispatch('changecampuslistvisibility', {'show': false})
		dispatch('clearresults', {});  
  }

  function resetFilter() {
    dispatch('changecampuslistvisibility', {'show': false})
		dispatch('clearresults', {}); 
    showInterestsForm = true
    level=""
  }

	function handleInterestSelection(si) {
      let data = [];
      data = programs.filter(program => {
        const programLevel = program.level === level ? true : false;
        if (!programLevel) return false;

        let programInterest = false;
        interestsSelected.forEach(selected => {
          if (program.interests.indexOf(selected) !== -1) {
            programInterest = true;
          }
        });

        return programInterest;
      });
      dispatch("processdata", { data: data });
  }
  
  $: { handleInterestSelection(interestsSelected) }
</script>

<div>
  <div>
    <h3 id="levellabel" class="control-label">Select your school level</h3>
    <div class="school-level-mobile">
      <select
        class="form-control"
        aria-labelledby="levellabel"
        name="level"
        id="level"
        bind:value={level}
        style="width:90%"
        on:change={getInterestsForLevel}
      >
        <option value>Select a level</option>
        <option value="Elementary">Elementary</option>
        <option value="Middle">Middle</option>
        <option value="High">High</option>
      </select>
    </div>
    <div class="school-level">
      <button type="button" on:click|preventDefault={() =>{ level='Elementary'; getInterestsForLevel()}}><span class="button-text">Elementary</span><span class="button-image"><img src="/images/elem.png" alt=""></span></button>
      <button type="button" on:click|preventDefault={() =>{ level='Middle'; getInterestsForLevel()}}><span class="button-text">Middle</span><span class="button-image"><img src="/images/middle.png" alt=""></span></button>
      <button type="button" on:click|preventDefault={() =>{ level='High'; getInterestsForLevel()}}><span class="button-text">High</span><span class="button-image"><img src="/images/high.png" alt=""></span></button>
    </div>

		{#if (level !== '')}
      {#if showInterestsForm}
      <div class="interests">
        <h2>Select your interests</h2>
        <div class="interest-checkbox-container">
          {#each interests as interest, i}
          <div class="interest-checkbox">
            <label>
              <img src="{getIcon(interest)}" alt="">
              <input
                type="checkbox"
                name="interestsSelected"
                value={interest}
                bind:group={interestsSelected}
              />
              {interest}
            </label>
          </div>
          {/each}
        </div>
        {#if interestsSelected.length}
        <button class="submit-interests" type="button" on:click|preventDefault={showInterestsWithCampus}>Submit</button>
        {/if}
      </div>
      {/if}
		{/if}

		{#if showInterestsForm !== true}
    <div class="showing-results-for">
      <span>Showing Results For</span>
      <span class="search-criteria"> "{interestsSelected.join(" | ")}"</span>
      <button class="reset-filters" type="button" on:click|preventDefault={resetFilter}>Reset Filters</button>
    </div>
		{/if}
  </div>
</div>