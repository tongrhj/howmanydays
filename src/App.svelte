<script>
	import pluralize from 'pluralize'
	import { DateTime } from 'luxon'

	import DateInput from './DateInput.svelte'

	const now = DateTime.local()
	const later = DateTime.local().plus({ days: 40 })

	// StagingDate = { year: number, month: string, day: number }
	let startDate /*StagingDate*/ = {
		year: now.year,
		month: now.monthLong,
		day: now.day,
	};
	let endDate /*StagingDate*/ = {
		year: later.year,
		month: later.monthLong,
		day: later.day
	}
	const setStartDate = (options) => {
		startDate = {
			...startDate,
			...options
		}
	}
	const setEndDate = (options) => {
		endDate = {
			...endDate,
			...options
		}
	}

	const MAP_SELECTED_DIFF_TO_FORMAT = [
		['days'],
		['years', 'months', 'days']
	]
	let selectedDiff = 0

	// Reference date: Used to get number of days in month
	const getReferenceDate = (d) => DateTime.fromFormat(`${d.month} ${d.year}`, 'LLLL yyyy');
	const getDateString = (d) => `${d.day} ${d.month} ${d.year}`

	const handleYearChange = (updateFn) => (event) => {
		const value = parseInt(event.target.value, 10)
		// Reference date expects 4 digit year
		if (value > 9999 || value < 1000 || !value) {
			updateFn({
				year: now.year
      })
		} else {
			updateFn({
				year: value
      })
    }
  }

  const handleMonthChange = (updateFn) => (date) => (event) => {
		if (getReferenceDate(date).daysInMonth < date.day) {
			updateFn({
				month: event.target.value,
				day: 1
			})
		} else {
			updateFn({
				month: event.target.value
			})
    }
  }

  const handleDayChange = (updateFn) => (event) => {
		const value = parseInt(event.target.value, 10)
    updateFn({
			day: value
		})
	}

	const handleDiffChange = (_) => {
		if (selectedDiff + 1 > MAP_SELECTED_DIFF_TO_FORMAT.length - 1) {
			selectedDiff = 0
		} else {
			selectedDiff += 1
		}
	}

	const makePositiveInt = (v) => v >= 0 ? v : (v * -1)

	// diffDuration = { days: number } | { days: number, months: number, years: number  }
	const formatDiffDuration = (diffDuration) => {
		const keys = Object.keys(diffDuration.toObject())
		const result = keys.reduce((acc, key) => {
			const value = makePositiveInt(diffDuration[key])
			if (value) {
				acc += `${acc.length ? ', ' : ''}${value} ${pluralize(key, value)}`
			}
			return acc
		}, '')
		return result.length ? result : '0 days'
	}

	const parseStagingDate = (d) => {
		return DateTime.fromFormat(`${d.day} ${d.month} ${d.year}`, 'd LLLL yyyy')
	}

	const flipOptions = {}

	// https://moment.github.io/luxon/docs/manual/math.html#casual-vs-longterm-conversion-accuracy
	$: diffDuration = parseStagingDate(endDate).diff(parseStagingDate(startDate), MAP_SELECTED_DIFF_TO_FORMAT[selectedDiff])
</script>

<main>
	<section class="app">
		<h3 class="answer"><span on:click={handleDiffChange}>{formatDiffDuration(diffDuration)}</span></h3>

		<h1 class="seperator">How Many Days between</h1>

		<div class="date">
			<DateInput
				dateDisplayed={startDate}
				referenceDate={getReferenceDate(startDate)}
				handleYearChange={handleYearChange(setStartDate)}
				handleMonthChange={handleMonthChange(setStartDate)(startDate)}
				handleDayChange={handleDayChange(setStartDate)}
			/>
		</div>

		<h3 class="seperator">and</h3>

		<div class="date">
			<DateInput
				dateDisplayed={endDate}
				referenceDate={getReferenceDate(endDate)}
				handleYearChange={handleYearChange(setEndDate)}
				handleMonthChange={handleMonthChange(setEndDate)(endDate)}
				handleDayChange={handleDayChange(setEndDate)}
			/>
		</div>
	</section>
</main>

<style>
.app {
	text-align: center;
	max-width: 30rem;
	margin: 0 auto;
	padding-bottom: var(--rhythm);
}
.date {
	border-radius: var(--radius);
	background-color: var(--brand-gray-1);
	margin: 0;
	padding: 0;
}
.seperator {
	margin-top: var(--rhythm);
	margin-bottom: var(--rhythm);
	font-size: 0.75rem;
	line-height: var(--tight);
}
.answer {
	font-size: 3.6rem;
	background: linear-gradient(71deg,#e8b4b8,#a49393);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
</style>
