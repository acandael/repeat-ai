<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import type { Content } from '@prismicio/client';
	import IconNpm from '~icons/fa6-brands/npm';
	import IconFigma from '~icons/fa6-brands/figma';
	import IconFly from '~icons/fa6-brands/fly';
	import IconCloudflare from '~icons/fa6-brands/cloudflare';
	import IconDigitalOcean from '~icons/fa6-brands/digital-ocean';
	import IconGithub from '~icons/fa6-brands/github';

	import background from './background.jpg';
	import Bounded from '$lib/components/Bounded.svelte';
	import LogoBackground from './LogoBackground.svelte';
	import StylizedLogoMark from './StylizedLogoMark.svelte';
	import { PrismicRichText, PrismicText } from '@prismicio/svelte';
	import clsx from 'clsx';

	export let slice: Content.IntegrationsSlice;

	const icons = {
		digitalocean: IconDigitalOcean,
		github: IconGithub,
		cloudflare: IconCloudflare,
		fly: IconFly,
		figma: IconFigma,
		npm: IconNpm
	};

	onMount(() => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce').matches;

		if (prefersReducedMotion) return;

		const tl = gsap.timeline({
			repeat: -1,
			defaults: { ease: 'power2.inOut' }
		});

		tl.to('.pulsing-logo', {
			keyframes: [
				{
					filter: 'brightness(2)',
					opacity: 1,
					duration: 0.4,
					ease: 'power2.in'
				},
				{ filter: 'brightness(1)', opacity: 0.7, duration: 0.9 }
			]
		});

		tl.to(
			'.signal-line',
			{
				keyframes: [
					{ backgroundPosition: '0% 0%' },
					{
						backgroundPosition: '100% 100%',
						stagger: { from: 'center', each: 0.3 },
						duration: 1
					}
				]
			},
			'-=1.4'
		);

		tl.to(
			'.pulsing-icon',
			{
				keyframes: [
					{
						opacity: 1,
						duration: 1,
						stagger: {
							from: 'center',
							each: 0.3
						}
					},
					{
						opacity: 0.4,
						duration: 1,
						stagger: {
							from: 'center',
							each: 0.3
						}
					}
				]
			},
			'-=2'
		);
	});
</script>

<Bounded
	data-slice-type={slice.slice_type}
	data-slice-variation={slice.variation}
	class="relative overflow-hidden"
>
	<img src={background} alt="" class="absolute inset-0 h-full w-full object-cover" />
	<LogoBackground />

	<div class="relative">
		<h2
			class="mx-auto max-w-2xl bg-gradient-to-b from-violet-50 to-violet-300 bg-clip-text py-2 text-center text-5xl font-medium text-balance text-transparent md:text-7xl"
		>
			<PrismicText field={slice.primary.heading} />
		</h2>
		<div class="mx-auto mt-6 max-w-md text-center text-balance text-gray-300">
			<PrismicRichText field={slice.primary.body} />
		</div>
		<div class="mt-20 flex flex-col items-center md:flex-row">
			{#each slice.primary.icons as item, index}
				{#if item.icon}
					{#if index === Math.floor(slice.primary.icons.length / 2)}
						<StylizedLogoMark />
						<div class="signal-line rotate-180" />
					{/if}

					<div
						class="pulsing-icon flex aspect-square shrink-0 items-center justify-center rounded-full border border-violet-50/30 bg-violet-50/25 p-3 text-3xl text-violet-100 opacity-40 md:text-3xl lg:text-5xl"
					>
						<svelte:component this={icons[item.icon]} />
					</div>

					{#if index !== slice.primary.icons.length - 1}
						<div
							class={clsx(
								'signal-line',
								index >= Math.floor(slice.primary.icons.length / 2) ? 'rotate-180' : 'rotate-0'
							)}
						/>
					{/if}
				{/if}
			{/each}
		</div>
	</div>
</Bounded>
