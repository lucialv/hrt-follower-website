<template>
	<div class="mt-8 mx-auto flex items-center justify-center">
		<div class="w-full">
			<p class="mt-2 text-center text-xl font-semibold text-white md:text-2xl lg:text-3xl">
				Llevo en hormonas
				<span class="font-bold text-emerald-400"> {{ daysElapsed }} días</span> y
				<span class="font-bold text-emerald-400">{{ hoursElapsed }} horas</span>.
			</p>
			<p class="mt-2 text-center text-xl font-semibold text-white md:text-2xl lg:text-3xl">
				El total de horas es <span class="font-bold text-emerald-400"> {{ totalHoursElapsed }} horas</span>.
			</p>
			<p class="mt-2 text-center text-xl font-semibold text-white md:text-2xl lg:text-3xl">
				El total de minutos es <span class="font-bold text-emerald-400"> {{ totalMinutesElapsed }} minutos</span> y segundos ni te cuento.
			</p>
			<h1 class="mb-4 mt-16 text-center text-xl font-semibold text-white md:text-2xl lg:text-3xl">
				<span class="align-bottom"
					>Cuánto <span class="font-bold text-emerald-400">queda</span> para que
					<span class="font-bold text-emerald-400">pase</span>...</span
				>
			</h1>
		</div>
	</div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const startDate = new Date('2023-05-20');
const endDate = new Date('3000-08-13');

const progressWidth = ref('0%');
const progress = ref(0);
const progressWithDecimals = ref('0.00');
const daysRemaining = ref(0);
const daysElapsed = ref(0);
const hoursElapsed = ref(0);
const totalHoursElapsed = ref(0);
const totalMinutesElapsed = ref(0);
const heartBeats = ref(0);
const blinkCount = ref(0);

const updateProgress = () => {
	const currentTime = new Date();

	if (currentTime >= endDate) {
		progressWidth.value = '100%';
		progress.value = 100;
		progressWithDecimals.value = progress.value.toFixed(2);
		daysRemaining.value = 0;
		return;
	}

	const totalTime = endDate - startDate;
	const elapsed = currentTime - startDate;
	const remainingTime = endDate - currentTime;
	const averageHeartRate = 80;
	const blinksPerMinute = 20;
	const remainingDays = Math.ceil(remainingTime / (1000 * 60 * 60 * 24));
	const elapsedDays = Math.floor(elapsed / (1000 * 60 * 60 * 24));
	const elapsedHours = Math.floor((elapsed / (1000 * 60 * 60)) % 24);
	const elapsedTotalHours = Math.floor(elapsed / (1000 * 60 * 60));
	const elapsedMinutes = Math.floor(elapsed / (1000 * 60));
	const totalMinutes = Math.floor(elapsed / (1000 * 60));
	const currentProgress = Math.min(100, (elapsed / totalTime) * 100);
	const blinkCountTotal = elapsedMinutes * blinksPerMinute * 2;
	heartBeats.value = elapsedMinutes * averageHeartRate * 2;
	progressWidth.value = `${currentProgress}%`;
	progress.value = currentProgress;
	daysElapsed.value = elapsedDays;
	hoursElapsed.value = elapsedHours;
	totalHoursElapsed.value = elapsedTotalHours;
	totalMinutesElapsed.value = totalMinutes;
	blinkCount.value = blinkCountTotal;
	progressWithDecimals.value = progress.value.toFixed(2);
	daysRemaining.value = remainingDays;
	requestAnimationFrame(updateProgress);
};

onMounted(() => {
	startDate.setHours(15, 0, 0);
	endDate.setHours(0, 0, 0);

	updateProgress();
});

onBeforeUnmount(() => {
	// No se necesita hacer nada aquí ya que la actualización del progreso se detendrá automáticamente cuando el componente se desmonte.
});
</script>
