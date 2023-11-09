<script lang="ts">
	const nodeBatchId18depth2days =
		'5516d8dcb2018f749dfd9e1590cf5afc19ac895a2706f3d0530d63d66e08d5ea';

	const fileHashZapaz1 = 'daa882683c3712146488ebe7282e208720b065257678687434fe37a6ed3ae741';
	const fileHashZapaz2 = '88cff91ddb09e053e4222a3309274fc6129ca2ac6d2f8370cdefa4c7b6c8849a';
	// const fileHash = '15afbaec7eaf77097e4f8ed031b5d1067c6a70dbf7168e1fb80d7745f1738b6c';

	const testBee = async () => {
		const responseAvailable = await fetch(`http://localhost:1633/stewardship/${fileHashZapaz2}`, {
			method: 'GET'
		});
		const jsonAvailable = await responseAvailable.json();
		console.log('testBee ~ responseAvailable:', jsonAvailable);

		if (!jsonAvailable.isRetrievable) return;

		////////////////////////////////////////////////////////////////

		// var headers = new Headers();
		// headers.append('swarm-postage-batch-id', nodeBatchId18depth2days);

		// const response = await fetch(`http://localhost:1633/stewardship/${fileHashZapaz2}`, {
		// 	method: 'PUT',
		// 	headers: headers
		// });

		// console.log('testBee ~ response:', response);

		// const data = await response.json();
		// console.log('testBee ~ data:', data);

		////////////////////////////////////////////////////////////////

		const responsePinCheck = await fetch(`http://localhost:1633/pins/${fileHashZapaz2}`, {
			method: 'GET'
		});
		console.log('testBee ~ responsePinCheck:', responsePinCheck);

		const jsonPinCheck = await responsePinCheck.json();
		console.log('testBee ~ responsePinCheck:', jsonPinCheck);

		if (!responsePinCheck.ok) {
			const responsePin = await fetch(`http://localhost:1633/pins/${fileHashZapaz2}`, {
				method: 'POST'
			});
			console.log('testBee ~ responsePin:', responsePin);

			const jsonPin = await responsePin.json();
			console.log('testBee ~ jsonPin:', jsonPin);
		}
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
