<script lang="ts">
	import { generateHueBackgroundGradient, generateChromaBackgroundGradient, generateToneBackgroundGradient } from '$lib/generateBackgrounds.ts';
	import * as Card from "$lib/components/ui/card";
    import { Hct } from '@material/material-color-utilities'
    import Color from 'color'
	import BackgroundColorDisplay from '$lib/BackgroundColorDisplay.svelte';
    import Slider from '$lib/components/ui/slider/slider.svelte';
    
	export let hue: number[] = [180];
	export let chroma: number[] = [50];
	export let tone: number[] = [50];
	
    $: argb = Hct.from(hue[0], chroma[0], tone[0]).argb
	$: hex = Color(argb).hex()

	let hexInput: HTMLInputElement

    const handleHexChange = (e: Event) => {
		const target = e.target as HTMLInputElement
		const value = target.value

		if (value !== hex) {
			try {
				const newArgbColor = Color(value).rgbNumber()
				const hct = Hct.fromInt(newArgbColor)

				hue[0] = hct.hue 
                chroma[0] = hct.chroma
                tone[0] = hct.tone
				hex = target.value
			} catch (error) {
			}
		}
	}

	$: hueBackground = generateHueBackgroundGradient(chroma[0], tone[0])

	$: chromaBackground = generateChromaBackgroundGradient(
		hue[0],
		tone[0]
	)

	$: toneBackground = generateToneBackgroundGradient(
		hue[0],
		chroma[0]
	)
  
</script>

<Card.Root class="w-[260px] ">
	<Card.Content>
		<div class="flex flex-col gap-1 pt-6 pb-2">
			<div
				class="aspect-rectangle h-[90px] w-[220px] rounded-sm border border-slate-300"
				style={`background-color: ${hex}`}
			/>
			<div class="flex justify-end">
				<input
				value={hex}
				name="color"
				class="ml-1 h-7 w-20 border-none px-1 py-1 text-sm"
				type="text"
				on:focusout={handleHexChange}
				bind:this={hexInput}
				/>
			</div>
			
		</div>
		<div class="flex flex-1 flex-col items-center gap-4">
			<div class="flex w-full w-[220px] flex-col gap-2">
				<div class="flex flex-row items-center justify-between">
					<span class="text-xs text-slate-500 font-bold">Hue</span>
					<input
						type="number"
						class="w-12 rounded-md bg-transparent px-1 text-xs border-slate-500"
						bind:value={hue}
					/>
				</div>
				<Slider
					min={0}
					max={360}
					background={hueBackground}
					bind:value={hue}
				/>
				<BackgroundColorDisplay background={hueBackground}/>
	
			</div>
			<div class="flex w-full max-w-[220px] flex-col gap-2">
				<div class="flex flex-row items-center  justify-between">
					<span class="text-xs text-slate-500 font-bold">Chroma</span>
					<input
						type="number"
						class="w-12 rounded-md bg-transparent px-1 text-xs"
						bind:value={chroma}
					/>
				</div>
				<Slider
					min={0}
					max={100}
					background={chromaBackground}
					bind:value={chroma}
				/>
				<BackgroundColorDisplay background={chromaBackground}/>
			</div>
			<div class="flex w-full max-w-[220px] flex-col gap-2">
				<div class="flex flex-row items-center justify-between">
					<span class="text-xs text-slate-500 font-bold">Tone</span>
					<input
						type="number"
						class="w-12 rounded-md bg-transparent px-1 text-xs"
						bind:value={tone}
					/>
				</div>
				<Slider
					min={0}
					max={100}
					background={toneBackground}
					bind:value={tone}
				/>
				<BackgroundColorDisplay background={toneBackground}/>
			</div>
		</div>
	</Card.Content>
</Card.Root>
  