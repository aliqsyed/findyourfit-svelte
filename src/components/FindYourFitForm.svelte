<script>
	import { createEventDispatcher } from 'svelte';
	import { onMount } from 'svelte';
import { prevent_default } from 'svelte/internal';

	const dispatch = createEventDispatcher();
	const dataurl = "http://gisd.test/api/find-your-fit"

	let programs = []
	let interests = []
	let interestsSelected = []
	let level = ""
  let showInterestsForm = true

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
 

	onMount(async () => {
			const response = await fetch(dataurl)
			const data = await response.json()
			programs = data;
     
	});

	function getInterestsForLevel() {
		interestsSelected = [];
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
	
		dispatch('clearresults', {
      text: "Hello"
    });
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
    <div class="school-level-mobile">
      <label for="level" class="control-label">Select your school level</label>
      <select
        class="form-control"
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
      <button type="button" on:click|preventDefault={() =>{ level='Elementary'; getInterestsForLevel()}}>Elementary</button>
      <button type="button" on:click|preventDefault={() =>{ level='Middle'; getInterestsForLevel()}}>Middle</button>
      <button type="button" on:click|preventDefault={() =>{ level='High'; getInterestsForLevel()}}>High</button>
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
        <button class="submit-interests" type="button" on:click|preventDefault={() => showInterestsForm = false}>Submit</button>
        {/if}
      </div>
      {/if}
		{/if}

		{#if interestsSelected.length}
    <div class="you-searched-for">
      <h2>You searched for:</h2>
      <div class="search-criteria">{ level } school { interestsSelected }</div>
    </div>
		{/if}
  </div>
</div>