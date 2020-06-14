<script>
  import { noop } from './helpers'
  import { Info } from 'luxon'
  export let dateDisplayed = {
    year: 1970,
    month: 'January',
    day: 1
  }
  export let handleYearChange
  export let handleMonthChange
  export let handleDayChange
  export let referenceDate

  // daysInMonth: [1,2,3...31]
  $: daysInMonth = referenceDate && referenceDate.isValid ? Array(referenceDate.daysInMonth).fill(1).map((x, y) => x + y) : []
</script>

<main>
  <form on:submit|preventDefault={noop}>
    <input type="number" name="year" min="1" max="9999" bind:value={dateDisplayed.year} on:change={handleYearChange} required />

    <select name="month" bind:value={dateDisplayed.month} on:change={handleMonthChange} required>
      <!-- Month in locale string: January, February, etc. -->
      {#each Info.months() as month, idx}
        <option key={idx} value={month}>
          {month}
        </option>
      {/each}
    </select>

    {#if daysInMonth.length > 0}
      <select name="day" bind:value={dateDisplayed.day} on:change={handleDayChange} required>
        {#each daysInMonth as day, idx}
          <option key={idx} value={day}>
            {day}
          </option>
        {/each}
      </select>
    {:else}
      <select name="day" bind:value={dateDisplayed.day} disabled>
        {#each daysInMonth as day, idx}
          <option key={idx} value={day}>
            {day}
          </option>
        {/each}
      </select>
    {/if}
  </form>
</main>

<style>
  form {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
  }
  input, select {
    line-height: var(--lh-base);
    padding: calc(var(--rhythm) * 0.75 - 1px) calc(var(--rhythm)*0.75);
    padding-right: var(--rhythm);
    border-radius: var(--radius);
    border-color: transparent;
    position: relative;
    outline: none;
    -webkit-appearance: none;
    -moz-appearance: textfield;
    font-weight: bold;
    display: inline-block;
    min-width: 4rem;
    text-overflow: ellipsis;
    text-align: center;
    transition: var(--transition-props) border;
    background-color: transparent;
    margin: 0;
    color: inherit;
    cursor: pointer;
  }
  input:focus, select:focus {
    border-color: var(--brand-colour);
  }
  select {
    background-image: var(--select-icon);
    background-position: calc(100% - (var(--rhythm) * 0.25)) calc(50% - 1px);
    background-repeat: no-repeat;
  }
  @media (prefers-color-scheme: dark) {
    input, select {
      color: var(--brand-colour);
    }
  }
</style>
