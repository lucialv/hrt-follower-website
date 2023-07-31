<template>
	<div class="mb-16 flex h-[10%] items-center justify-center">
		<div class="w-full">
			<div class="flex justify-center mb-4">
				<h1 class="mb-4 whitespace-nowrap text-center text-xl text-white md:text-2xl">
					<span class="inline-block rounded-2xl bg-black/5 px-4 py-4 font-semibold text-white ring-1 ring-orange-400"><span class="text-white font-bold">Máximo <span class="text-orange-400">(1-2 años)</span></span><br /> <br />Disminución massa muscular/fuerza </span>
				</h1>
			</div>
			<div class="relative h-4 w-1/2 mx-auto overflow-hidden rounded-full bg-white">
				<div class="animate-stripes absolute left-0 top-0 h-full rounded-lg bg-orange-400" :style="{ width: progressWidth }"></div>
			</div>
			<p class="mt-2 text-center text-2xl font-semibold text-white">
				<span class="font-bold text-orange-400">{{ progressWithDecimals.slice(0, -3) }}</span
				>{{ progressWithDecimals.slice(-3) }}
				<span class="font-bold text-orange-400"> %</span>
			</p>
			<p v-if="iniciado && !finalizado" class="mt-2 text-center text-xl md:text-2xl font-semibold text-white">
				<span  class="font-bold text-orange-400">{{ daysRemaining }} días</span> restantes
			</p>
			<p v-else-if="!iniciado && !finalizado" class="mt-2 text-center text-xl md:text-2xl font-semibold text-white">
				<span  class="font-bold text-orange-400">{{ startDaysRemaining }} días</span> para empezar
			</p>
		</div>
	</div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const startDate = new Date('2024-05-20');
const endDate = new Date('2025-05-20');

const progressWidth = ref('0%');
const progress = ref(0);
const progressWithDecimals = ref('0.00');
const daysRemaining = ref(0);
const startDaysRemaining = ref(0);
const iniciado = ref(true);
const finalizado = ref(false);

const updateProgress = () => {
	const currentTime = new Date();

	if (progress.value < 0) {
		progressWidth.value = '0%';
		progress.value = 0;
		progressWithDecimals.value = progress.value.toFixed(0);
		daysRemaining.value = 0;
		const remainingStartTime = startDate - currentTime;
		const remainingStartDays = Math.ceil(remainingStartTime / (1000 * 60 * 60 * 24));
		startDaysRemaining.value = remainingStartDays;
		const remainingTime = endDate - currentTime;
		const remainingDays = Math.ceil(remainingTime / (1000 * 60 * 60 * 24));
		daysRemaining.value = remainingDays;
		iniciado.value = false;
		return;
	}
	
	if (currentTime >= endDate) {
		progressWidth.value = '100%';
		progress.value = 100;
		progressWithDecimals.value = progress.value.toFixed(0);
		daysRemaining.value = 0;
		finalizado.value = true;
		return;
	}

	const totalTime = endDate - startDate;
	const elapsed = currentTime - startDate;
	const remainingTime = endDate - currentTime;
	const remainingDays = Math.ceil(remainingTime / (1000 * 60 * 60 * 24));
	const currentProgress = Math.min(100, (elapsed / totalTime) * 100);
	progressWidth.value = `${currentProgress}%`;
	progress.value = currentProgress;
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

<style>
.animate-stripes {
	animation: stripes 1s linear infinite;
	background-image: linear-gradient(
		45deg,
		rgba(255, 255, 255, 0.2) 25%,
		transparent 25%,
		transparent 50%,
		rgba(255, 255, 255, 0.2) 50%,
		rgba(255, 255, 255, 0.2) 75%,
		transparent 75%,
		transparent
	);
	background-size: 1rem 1rem;
}

@keyframes stripes {
	100% {
		background-position: 1rem 0;
	}
}
</style>

