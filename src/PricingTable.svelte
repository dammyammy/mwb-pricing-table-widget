<svelte:options tag="pricing-table" />

<script>
    import { onMount } from 'svelte';
    
	let plans;

    let sections;

    let monthly = false;

    export let switchable = false;

    
    export let data = 'https://corsanywhere.herokuapp.com/https://raw.githubusercontent.com/dammyammy/madewithbree-widgets/9a33d198298fe2df1b2200126775f8e2cc9fdf63/pricing-table/pricing-table.json';

    function setIcon(feature){
        return feature 	
            ? `<span>
                <svg height=".9em" width=".9em" fill="currentColor" viewBox="0 0 516 516">
                    <path d="M0 274l177 177 339-339-49-48-290 290L48 226z"/>
                </svg>
            </span>` 

            : `<span>
                <svg height=".9em" width=".9em" fill="currentColor" viewBox="0 0 612 612">
                    <path d="M612 36L577 1 306 271 35 1 0 36l271 270L0 576l35 35 271-270 271 270 35-35-271-270z"/>
                </svg>
            </span>`;
    }

	onMount(async () => {
		const res = await fetch(data);
        const response = await res.json();

		plans = response.plans
		sections = response.sections
	});
</script>


{#if sections && plans}
<div class="pricing-table is-comparative">
    <div class="pricing-plan is-features">
        <div class="plan-header"></div>
        
        <div class="plan-price">
          <span class="plan-price-amount">&nbsp;</span>
          {#if switchable === true}
              <div class="toggle-container">
                <div class="toggle">
                    <span class="text {monthly ? '' : 'is-active'}">Pay Annually</span>
                    <label class="switch">
                        <input type="checkbox" aria-label="toggle plan period" bind:checked={monthly}>
                        <span class="slider"></span>
                    </label>
                    <span class="text {monthly ? 'is-active' : ''}">Pay Monthly</span>
                </div>
                <div class="subtext">Prices do not include applicable taxes.</div>
                <div class="subtext savings">Save up to 20% by paying annually</div>
            </div>
            {:else}
            <div class="toggle-container" style="margin-bottom: 48px"></div>
          {/if}
        </div>
        
        {#each sections as section}
            <div class="plan-header has-text-left">{section.title}</div>
            <div class="plan-items">
                {#each section.features as feature}
                    <div class="plan-item has-text-left">
                        {feature.title}
                        {#if feature.description !== null}
                            <span 
                            class="has-tooltip-multiline has-tooltip-right" 
                            data-tooltip={feature.description}>
                                <span>
                                    <svg height="0.9em" width="0.9em" fill="#777676" viewBox="0 0 512 512">
                                        <path d="M256 0A255.9 255.9 0 000 256c0 141.5 114.5 256 256 256s256-114.5 256-256S397.5 0 256 0zm0 476.3c-121.5 0-220.3-98.8-220.3-220.3S134.5 35.7 256 35.7c121.5 0 220.3 98.8 220.3 220.3S377.5 476.3 256 476.3z"/>
                                        <path d="M248.4 324a25.9 25.9 0 00-25.6 25.9c0 13.8 11.1 26 25.6 26 14.5 0 26-12.2 26-26 0-14.2-11.8-26-26-26zM252.8 127.5c-45.5 0-66.4 27-66.4 45.1 0 13.2 11.1 19.2 20.2 19.2 18.2 0 10.8-26 45.2-26 16.8 0 30.3 7.5 30.3 23 0 18.2-18.8 28.6-30 38a61.7 61.7 0 00-22.5 51.3c0 17.5 4.7 22.6 18.5 22.6 16.5 0 19.9-7.4 19.9-13.8 0-17.6.3-27.7 18.8-42.2 9.1-7 37.8-30 37.8-61.6s-28.7-55.6-71.8-55.6z"/>
                                    </svg>
                                </span>
                            </span>
                        {/if}
                    </div>
                {/each}
            </div>
        {/each}
        <div class="plan-footer"></div>
    </div>

    {#each plans as plan, key}
        <div class="pricing-plan plan {plan.color} {plan.popular !== undefined && plan.popular ? 'is-popular': ''}">
            {#if plan.popular !== undefined && plan.popular }
                <div class="most-popular-label">Most Popular</div>
            {:else}
                <div class="most-popular-label" style="opacity:0">Most Popular</div>
            {/if}

            <div class="plan-header">{plan.title}</div>

            <div class="plan-price">
                <span class="plan-price-amount">
                    <span class="plan-price-currency">{plan.currency}</span>
                    {(switchable === true &&  monthly === true) || monthly === true ? plan.price.monthly : plan.price.yearly}
                </span>

                {#if switchable === true }
                    {#if monthly === false }
                        <div>Save {plan.currency}{plan.price.yearly_savings} annually</div>
                    {:else}
                        <div style="opacity:0">_</div>
                    {/if}
                {/if}
            </div>
                    
            {#each sections as section}
                <div class="plan-header">&nbsp;</div>
    
                {#each section.features as feature}
                <div class="plan-item" data-feature={feature.title}>
                    {#if key === 0}
                        {#if typeof feature.items.lite === 'boolean' }
                            {@html setIcon(feature.items.lite)}
                        {:else}
                            {feature.items.lite !== null ? feature.items.lite : '-'}
                        {/if}
                    {:else if key === 1}
                        {#if typeof feature.items.business === 'boolean' }
                            {@html setIcon(feature.items.business)}
                        {:else}
                            {feature.items.business !== null ? feature.items.business : '-'}
                        {/if}
                    {:else}
                        {#if typeof feature.items.premium === 'boolean' }
                            {@html setIcon(feature.items.premium)}
                        {:else}
                            {feature.items.premium !== null ? feature.items.premium : '-'}
                        {/if}
                    {/if}
                </div>
                {/each}
            {/each}
    
            <div class="plan-footer">
            {#if plan.button !== undefined }
            <a href={plan.url} class="button {plan.color}">
                {plan.button || 'Choose'}
            </a>
            {/if}
           
            </div>
        </div>
    {/each}
</div>
{/if}

<style lang="scss" src="./app.scss"></style>

