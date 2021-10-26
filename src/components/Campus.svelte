<script>
	export let campus
	const imagePath = "/images/"
	function getProgramUrl(program) {
		return program.webpage === ""
			? `/node/${program.program_id}`
			: program.webpage;
  }
</script>	


{#if campus.programs.length || campus.ptechpathway.length }
  <div class="campus">
    <div class="campus-details">
      <h3 class="campus-name">{campus.campusinfo.title}</h3>
      <div class="campus-contact-info">
        <div>
					<span class="address-label">Address: </span><span class="address-link"><a href="{campus.campusinfo.maplink}">{campus.campusinfo.address}, {campus.campusinfo.city} TX, {campus.campusinfo.zipcode}</a></span>
				</div>
        <div>
					<span class="email-label">Email: </span><span class="email-link"><a href={`mailto:${campus.campusinfo.school_email}`}>{campus.campusinfo.school_email}</a></span>
					<span class="phone-label">Phone: </span><span class="phone-link"><a href={`tel:${campus.campusinfo.phone}`}>{campus.campusinfo.phone}</a></span>
					<span class="grade-label">Grade: </span><span class="grade-link">{campus.campusinfo.starting_grade}&ndash;{campus.campusinfo.ending_grade}</span>
				</div>
      </div>

			{#each campus.programs as program, i}
				<div class="program-details">
					<div class="magnet-icon">
						{#if program.is_magnet === true}
						<img src={`${imagePath}magnet-star.png`} alt />
						{/if}
					</div>
					<div class="program-info">
						<h4 class="program-name">
							<a href="{getProgramUrl(program)}" target="_blank">{ program.title }</a>
						</h4>
						{#if program.is_magnet}
						<span class="magnet-notice">Magnet application required</span>
						{/if}
					</div>
				</div>
				{/each}

			{#if campus.ptechpathway.length}
      <div>
        <div class="program-details">
          <div class="magnet-icon">
            <img src={`${imagePath}magnet-star.png`} alt />
          </div>
					{#each campus.ptech as tech, j}
          <div class="program-info">
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
		<div class="campus-mascot">
        <img
          src="{campus.campusinfo.mascot.src}"
          alt="{campus.campusinfo.mascot.alt}"
          class="campus-mascot-img"
        />   
    </div>
  </div>
{/if}
