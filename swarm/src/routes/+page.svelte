<script lang="ts">
	const nodeBatchId18depth2days =
		'5516d8dcb2018f749dfd9e1590cf5afc19ac895a2706f3d0530d63d66e08d5ea';

	let urls = [
		'https://ipfs.io/ipfs/bafybeignamxpftgmvoowon6w5cgkrunowaopizekielfjeth5fsmwgrv3i',
		'https://bafkreidy4ittmzdpprothuzjkrf77ifm5ezmgst6biqbrsfbifwdvbrx6e.ipfs.nftstorage.link'
	];

	const fetchFiles = async (urls: string[]) => {
		let fetchPromises = urls.map((url) => {
			return fetch(url)
				.then((response) => {
					if (!response.ok) {
						throw new Error(`Erreur lors de la récupération de ${url}: ${response.statusText}`);
					}
					return response.blob();
				})
				.catch((error) => console.error("Erreur lors de la récupération d'une URL:", error));
		});

		let responses = await Promise.all(fetchPromises);
		console.log('fetchFiles ~ responses:', responses);

		let blobs = await Promise.all(fetchPromises);

		blobs = blobs.filter((blob) => blob !== undefined);
		console.log('fetchFiles ~ blobs:', blobs);

		let files = blobs.map(
			(blob, index) => new File([blob as Blob], `file${index}`, { type: (blob as Blob).type })
		);
		console.log('fetchFiles ~ files:', files);

		return files;
	};

	const filesToBase64 = (files: File[]) => {
		return Promise.all(
			files.map((file) => {
				return new Promise((resolve, reject) => {
					const reader = new FileReader();
					reader.onloadend = () => resolve(reader.result);
					reader.onerror = reject;
					reader.readAsDataURL(file);
				});
			})
		);
	};

	const testBee = async () => {
		let filesToupload = await fetchFiles(urls);
		console.log('testBee ~ filesToupload:', filesToupload);

		let files64 = await filesToBase64(filesToupload);
		console.log('testBee ~ files64:', files64);

		let xhr = new XMLHttpRequest();
		xhr.open('POST', 'http://localhost:1633/bzz', true);
		xhr.responseType = 'json';

		let boundary = '----WebKitFormBoundary' + Math.random().toString().substr(2);
		let contentType = 'multipart/form-data; boundary=' + boundary;

		let body = '';
		filesToupload.forEach((file, index) => {
			body += '--' + boundary + '\r\n';
			body +=
				'Content-Disposition: form-data; name="file' +
				index +
				'"; filename="' +
				file.name +
				'"\r\n';
			body += 'Content-Type: ' + file.type + '\r\n';
			body += 'Content-Length: ' + file.size + '\r\n';
			body += '\r\n';
			body += files64[index];
			body += '\r\n';
		});
		body += '--' + boundary + '--';

		xhr.setRequestHeader('Content-Type', contentType);
		xhr.setRequestHeader('swarm-postage-batch-id', nodeBatchId18depth2days);
		xhr.setRequestHeader('swarm-pin', 'true');
		xhr.setRequestHeader('swarm-collection', 'true');

		xhr.onload = function () {
			if (xhr.status === 201) {
				console.log(xhr.response);
			} else {
				console.error('Erreur lors de la requête : ' + xhr.status);
			}
		};

		xhr.onerror = function () {
			console.error('Une erreur est survenue lors de la requête');
		};

		xhr.send(body);
	};
	// c2c90a0677f467f5e0011df4095ca757854c2e6de8275564dc41d4d18023a2ff
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
