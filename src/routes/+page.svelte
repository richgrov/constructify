<script lang="ts">
	import '../app.css';
	import Search from '../lib/search.svelte';
	import '../firebase';
	import { getFunctions, httpsCallable, connectFunctionsEmulator } from 'firebase/functions';
	import { goto } from '$app/navigation';

	const functions = getFunctions();
	const generate = httpsCallable<{ prompt: string }, { jobId: string }>(functions, 'generate');
	if (import.meta.env['MODE'] === 'development') {
		connectFunctionsEmulator(functions, 'localhost', 5001);
	}

	let loading = false;
	async function submit(prompt: string) {
		loading = true;

		try {
			const result = await generate({ prompt });
			const jobId = result.data.jobId;
			loading = false;
			goto(`/view?id=${jobId}`);
		} catch (err) {
			console.error(err);
		} finally {
			loading = false;
		}
	}
</script>

<div style="background: url('/background.png');" class="h-full">
	<div class="flex justify-center items-center flex-col h-4/5">
		<h1 class="text-white text-5xl pb-10">constructify!</h1>
		<div class="w-2/5">
			<Search onSubmit={submit} loading={loading} />
		</div>
	</div>
</div>
