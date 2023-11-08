<script>
	import { Bee } from '@ethersphere/bee-js';

	const bee = new Bee('http://localhost:1633');

	const nodeBatchId6h = '6a7213e98c051e95d796fe1fd053321c21fd0a24f1dfa289cedab8fa3c6b1f9c';
	const nodeBatchId2j = 'bcec78baa7ef7f05c967d521c16da8969461fc1fce7a81128e37f763921b5147';
	const beeReference = 'fd79d5e0ebd8407e422f53ce1d7c4c41ebf403be55143900f8d1490560294780';
	const fileReference = 'a4a41ca8dbf3ffd5ad1673278f477a9b79ba74c0bb709545ef7fe1258244f46f';
	const twoDayFileRef = 'bc6361e872f78e04df49ca3904cac8ece8394b20e6cfaa3712e9161aaf5e2ab9';
	// ad8a3d076205e768ad7a21ff2de1248f64b684006f7634f51fd0fd00314edead
	// ad8a3d076205e768ad7a21ff2de1248f64b684006f7634f51fd0fd00314edead

	// const testBee = async () => {
	// 	const response = await fetch(
	// 		'https://api.gateway.ethswarm.org/bzz/ac8445cf1fd7999eec135ef087b9cfeebfd2297734eee64092381da1ddc3e2d8'
	// 	);
	// 	console.log('testBee ~ response:', response);
	// 	const contentDisposition = response.headers.get('Content-Disposition');
	// 	const fileName = contentDisposition.split('filename=')[1];
	// 	console.log(fileName); // Affiche "myfile.txt"

	// 	const blob = await response.blob();
	// 	console.log('testBee ~ blob:', blob);

	// 	var headers = new Headers();
	// 	headers.append('swarm-postage-batch-id', nodeBatchId2j);
	// 	headers.append('swarm-pin', 'true');
	// 	// headers.append('Authorization', 'Bearer votre_token');

	// 	fetch('http://localhost:1633/bytes', {
	// 		method: 'POST',
	// 		headers: headers,
	// 		body: blob
	// 	})
	// 		.then((response) => {
	// 			console.log('testBee ~ response:', response);
	// 			return response.json();
	// 		})
	// 		.then((data) => console.log(data))
	// 		.catch((error) => console.error('Erreur:', error));
	// };

	let urls = [
		'https://ipfs.io/ipfs/bafybeignamxpftgmvoowon6w5cgkrunowaopizekielfjeth5fsmwgrv3i',
		'https://bafkreidy4ittmzdpprothuzjkrf77ifm5ezmgst6biqbrsfbifwdvbrx6e.ipfs.nftstorage.link'
	];

	const fetchFiles = async (urls) => {
		let fetchPromises = urls.map((url) => {
			return fetch(url)
				.then((response) => {
					if (!response.ok) {
						throw new Error(`Erreur lors de la récupération de ${url}: ${response.statusText}`);
					}
					return response.blob(); // on retourne un Blob au lieu de la réponse directement
				})
				.catch((error) => console.error("Erreur lors de la récupération d'une URL:", error));
		});

		// On attend que toutes les requêtes soient terminées
		let responses = await Promise.all(fetchPromises);
		console.log('fetchFiles ~ responses:', responses);

		// On récupère les données des réponses sous forme de Blob
		let blobs = await Promise.all(fetchPromises);

		blobs = blobs.filter((blob) => blob !== undefined);
		console.log('fetchFiles ~ blobs:', blobs);

		// On convertit les Blob en File (on suppose que chaque URL est le nom du fichier)
		let files = blobs.map((blob, index) => new File([blob], `file${index}`));

		return files;
	};

	let formData = new FormData();

	const prepareFiles = async () => {
		let filesToupload = await fetchFiles(urls);
		console.log('prepareFiles ~ files:', filesToupload);

		for (let i = 0; i < filesToupload.length; i++) {
			formData.append(`file${i}`, filesToupload[i]);
		}
		// return filesToupload;
	};

	const testBee = async () => {
		// let filesArray = await prepareFiles();
		// console.log('testBee ~ filesArray:', filesArray);
		await prepareFiles();
		console.log('testBee ~ prepareFiles:', formData);

		console.log('file0', formData.get('file0'));
		console.log('file1', formData.get('file1'));

		// const result = await bee.uploadFiles(nodeBatchId2j, filesArray);
		// console.log('testBee ~ result:', result);

		var headers = new Headers();
		headers.append('swarm-postage-batch-id', nodeBatchId2j);
		headers.append('swarm-pin', 'true');
		headers.append('swarm-collection', 'true');

		fetch('http://localhost:1633/bzz', {
			method: 'POST',
			headers: headers,
			body: formData
		})
			.then((response) => {
				console.log('testBee ~ response:', response);
				return response.json();
			})
			.then((data) => console.log(data))
			.catch((error) => console.error('Erreur:', error));
	};

	// "dd9040933d2552deab44b458b2afcf3e0fc45fcdcd3d0d154889c68ca78774a2"

	
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Test BeeJS</button>
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
