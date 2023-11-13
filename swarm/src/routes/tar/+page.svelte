<script lang="ts">
	import { makeTar, type Collection } from '$lib/ts/tar';

	const batchId = '241856bcc2267a69e13de19112aee051191b5afd3b579afcc8923d532994dd17';
	const urlImage =
		'https://ipfs.io/ipfs/bafkreia756ojs2x7ld7m6jjlz7djojgonh4sgnqotxsiorhux3az6v45ly';
	const urlText =
		'https://ipfs.io/ipfs/bafkreieivwe2vhxx72iqbjibxabk5net4ah5lo3khekt6ojyn7cucek624';

	const url = 'http://bee.swarm.public.dappnode:1633/bzz';

	const testBee = async () => {
		const blobText = await (await fetch(urlText)).blob();
		const uint8ArrayText = new Uint8Array(await blobText.arrayBuffer());

		const blobImage = await (await fetch(urlImage)).blob();
		const uint8ArrayImage = new Uint8Array(await blobImage.arrayBuffer());

		const collection = [
			{ data: uint8ArrayText, path: 'metadata.json' },
			{ data: uint8ArrayImage, path: 'content.png' }
		];
		const body = makeTar(collection);

		const headers = new Headers();
		headers.append('Content-Type', 'application/x-tar');
		headers.append('Swarm-Postage-Batch-Id', batchId);
		headers.append('Swarm-Pin', 'true');
		headers.append('Swarm-Collection', 'true');

		const response = await fetch(url, { method: 'POST', headers, body });

		console.log('testBee ~ response:', await response.json());
	};
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Test BeeJS</button>
</section>
