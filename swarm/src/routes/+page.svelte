<script lang="ts">
	const nodeBatchId18depth2days =
		'5516d8dcb2018f749dfd9e1590cf5afc19ac895a2706f3d0530d63d66e08d5ea';

	const fileHash = '';

	const testBee = async () => {
		var headers = new Headers();
		headers.append('swarm-postage-batch-id', nodeBatchId18depth2days);

		const response = await fetch(`http://localhost:1633/stewardship/${fileHash}`, {
			method: 'PUT',
			headers: headers
		});

		console.log('testBee ~ response:', response);

		const data = response.json();
		console.log('testBee ~ data:', data);
	};
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Test collection</button>
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}
</style>
