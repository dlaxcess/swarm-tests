<script lang="ts">
	const batchId = '241856bcc2267a69e13de19112aee051191b5afd3b579afcc8923d532994dd17';
	const urlImage =
		'https://ipfs.io/ipfs/bafkreia756ojs2x7ld7m6jjlz7djojgonh4sgnqotxsiorhux3az6v45ly';
	const urlText =
		'https://ipfs.io/ipfs/bafkreieivwe2vhxx72iqbjibxabk5net4ah5lo3khekt6ojyn7cucek624';
	const url = 'http://bee.swarm.public.dappnode:1633/bzz';

	const testBee = async () => {
		const text = await (await fetch(urlText)).text();
		const blobImage = await (await fetch(urlImage)).blob();

		const body = new FormData();
		body.append('text', text);
		body.append('file0', blobImage, 'file0');

		const headers = new Headers();
		headers.append('swarm-postage-batch-id', batchId);
		headers.append('swarm-pin', 'true');
		headers.append('swarm-collection', 'true');

		const response = await fetch(url, { method: 'POST', headers, body });

		console.log('testBee ~ response:', await response.json());
	};
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Test BeeJS</button>
</section>
