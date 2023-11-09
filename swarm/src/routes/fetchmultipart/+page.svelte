<script lang="ts">
	const nodeBatchId18depth2days =
		'5516d8dcb2018f749dfd9e1590cf5afc19ac895a2706f3d0530d63d66e08d5ea';

	let urls = [
		'https://ipfs.io/ipfs/bafkreifb7sh53wfm4pui3b4itmi5wrlfldzfpoqkyiraru7wnpo5lnmvx4',
		// 'https://ipfs.io/ipfs/bafybeignamxpftgmvoowon6w5cgkrunowaopizekielfjeth5fsmwgrv3i',
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
				.catch((error) => {
					console.error("Erreur lors de la récupération d'une URL:", error);
					return;
				});
		});

		let responses = await Promise.all(fetchPromises);
		console.log('fetchFiles ~ responses:', responses);

		let blobs = await Promise.all(fetchPromises);

		blobs = blobs.filter((blob) => blob !== undefined);
		console.log('fetchFiles ~ blobs:', blobs);

		return blobs;
	};

	// function convertFilesToDataURLs(files: File[]) {
	// 	let promises = files.map((file) => {
	// 		return new Promise((resolve, reject) => {
	// 			let reader = new FileReader();
	// 			reader.onload = function (event) {
	// 				resolve(event.target?.result);
	// 			};
	// 			reader.onerror = reject;
	// 			reader.readAsDataURL(file);
	// 		});
	// 	});
	// 	return Promise.all(promises);
	// }

	function convertFilesToArrayBuffers(files: File[]) {
		let promises = files.map((file) => {
			return new Promise((resolve, reject) => {
				let reader = new FileReader();
				reader.onload = function (event) {
					resolve(event.target?.result);
				};
				reader.onerror = reject;
				reader.readAsArrayBuffer(file);
			});
		});
		return Promise.all(promises);
	}

	function arrayBufferToString(buffer: ArrayBuffer) {
		let decoder = new TextDecoder('utf-8');
		return decoder.decode(buffer);
	}
    

	// function calculateBase64Size(base64String: string) {
	// 	let padding;
	// 	if (base64String.endsWith('==')) {
	// 		padding = 2;
	// 	} else if (base64String.endsWith('=')) {
	// 		padding = 1;
	// 	} else {
	// 		padding = 0;
	// 	}

	// 	let fileSize = Math.ceil((base64String.length - padding) * (3 / 4));
	// 	return fileSize;
	// }

	const testBee = async () => {
		let blobsToupload = await fetchFiles(urls);

		let filesToupload = blobsToupload.map(
			(blob, index) => new File([blob as Blob], `file${index}.jpg`, { type: (blob as Blob).type })
		);
		console.log('fetchFiles ~ files:', filesToupload);

		let filesBinary: ArrayBuffer[] = await convertFilesToArrayBuffers(filesToupload);
		console.log('testBee ~ files64:', filesBinary);

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
			body += arrayBufferToString(filesBinary[index]);
			body += '\r\n';
		});
		body += '--' + boundary + '--';

		console.log('testBee ~ body:', body);

		let headers = new Headers();
		headers.append('Content-Type', contentType);
		headers.append('swarm-postage-batch-id', nodeBatchId18depth2days);
		headers.append('swarm-pin', 'true');
		headers.append('swarm-collection', 'true');

		const response = await fetch('http://localhost:1633/bzz', {
			method: 'POST',
			headers: headers,
			body: body
		});
		console.log('testBee ~ response:', await response.json());
	};
	// 76886a9b493604cb4e42da07056a1c2338bc3e60e04bb87f97745b69ca1e3e38
	// c2c90a0677f467f5e0011df4095ca757854c2e6de8275564dc41d4d18023a2ff
	// 0f87e63f65d35584bd2cf442c250118de14fe584f6b486fb434bdd0161bf2dd1
	// 4c995ccdcdc3a642aa0b71629d816151151f40443816e4fe2d75b7a04cb1d9fa
	// 2393c65e9e3434ed71836b68286d1fb8729539d51c53ac08f09731642694cdc5
	// 24fe909d0abdf7d269b609b13bbb540bb406b2141b9677945d8ee5503fdacadd
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
