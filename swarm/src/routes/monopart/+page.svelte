<script lang="ts">
	const batchId = '241856bcc2267a69e13de19112aee051191b5afd3b579afcc8923d532994dd17';
	const urlImage =
		'https://ipfs.io/ipfs/bafkreia756ojs2x7ld7m6jjlz7djojgonh4sgnqotxsiorhux3az6v45ly';
	const url = 'http://bee.swarm.public.dappnode:1633/bzz';

	const testBee = async () => {
		const body = await (await fetch(urlImage)).blob();
		console.log('testBee ~ body.size:', body.size);

		let headers = new Headers();
		headers.append('Content-Type', 'image/jpeg');
		headers.append('swarm-postage-batch-id', batchId);

		const response = await fetch(url, { method: 'POST', headers, body });

		console.log('testBee ~ response:', await response.json());
	};
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Test BeeJS</button>
</section>
