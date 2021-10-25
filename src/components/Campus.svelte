<script>
	export let campus

	function getProgramUrl(program) {
		return program.webpage === ""
			? `/node/${program.program_id}`
			: program.webpage;
  }
</script>	

<div>
	{#if campus.programs.length || campus.ptechpathway.length }
  <div class="row campusrow">
    <div class="col-md-2">
      <div class="center-block">
        <img
          src="{campus.campusinfo.mascot.src}"
          alt="{campus.campusinfo.mascot.alt}"
          class="mascot"
        />
      </div>
    </div>
    <div class="col-md-10">
      <h3>{campus.campusinfo.title}</h3>
      <div>
        {campus.campusinfo.address}, {campus.campusinfo.city} TX,
        {campus.campusinfo.zipcode}
        <br />
        <a href={`tel:${campus.campusinfo.phone}`}>{campus.campusinfo.phone}</a>
      </div>

		{#each campus.programs as program, i}
      <div class="program-details">
        <div>
          <img v-if="program.is_magnet" src="https://www.garlandisd.net/file/12101" alt />
        </div>
        <div>
          <h4 class="program-name">
            <a href="{getProgramUrl(program)}" target="_blank">{ program.title }</a>
          </h4>
					{#if program.is_magnet}
          <span class="magnet_notice">Magnet application required</span>
					{/if}
        </div>
      </div>
			{/each}


			{#if campus.ptechpathway.length}
      <div>
        <div class="program-details">
          <div>
            <img src="https://www.garlandisd.net/file/12101" alt />
          </div>
					{#each campus.ptech as tech, j}
          <div>
            <h4 class="program-name ptech-name">
              <a href="{getProgramUrl(tech)}" target="_blank">{ tech.title }</a>
            </h4>
						{#if tech.is_magnet}
            <span class="magnet_notice">Magnet application required</span>
						{/if}
            <div>The following degree programs are available for P-TECH at this campus:</div>
          </div>
					{/each}
        </div>

        <ul class="pathway-list">
				{#each campus.ptechpathway as pathway, k}
          <li class="pathway-name" >
            <a href="{getProgramUrl(pathway)}" target="_blank">{ pathway.title }</a>
						{#if pathway.is_magnet}
            <span class="magnet_notice">Magnet application required</span>
						{/if}
          </li>
				{/each}	
        </ul>
      </div>
			{/if}
      <div class="learn-more">
        Learn more about
        <a
          href={`/node/${campus.campusinfo.nid}`}
          target="_blank"
        >{campus.campusinfo.friendly_name}</a>.
      </div>
    </div>
  </div>
	{/if}
</div>
