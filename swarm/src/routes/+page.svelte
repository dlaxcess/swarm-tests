<script lang="ts">
	const nodeBatchId18depth2days =
		'5516d8dcb2018f749dfd9e1590cf5afc19ac895a2706f3d0530d63d66e08d5ea';

	let urls = [
		'https://ipfs.io/ipfs/bafybeignamxpftgmvoowon6w5cgkrunowaopizekielfjeth5fsmwgrv3i',
		'https://bafkreidy4ittmzdpprothuzjkrf77ifm5ezmgst6biqbrsfbifwdvbrx6e.ipfs.nftstorage.link'
	];

	let filesSize = 0;

	const calculateTotalSize = (files: File[]) => {
		let totalSize = 0;
		for (let file of files) {
			totalSize += file.size;
		}
		return totalSize;
	};

	const fetchFiles = async (urls: string[]) => {
		let fetchPromises = urls.map((url) => {
			return fetch(url)
				.then((response) => {
					if (!response.ok) {
						throw new Error(`Error getting : ${url}: ${response.statusText}`);
					}
					return response.blob();
				})
				.catch((error) => console.error('Error while geting an URL:', error));
		});

		let blobs = await Promise.all(fetchPromises);

		blobs = blobs.filter((blob) => blob !== undefined);
		console.log('fetchFiles ~ blobs:', blobs);

		let files = blobs.map(
			(blob, index) => new File([blob as Blob], `file${index}`, { type: (blob as Blob).type })
		);

		filesSize = calculateTotalSize(files);

		return files;
	};

	const prepareFiles = async () => {
		let formData = new FormData();

		let filesToupload = await fetchFiles(urls);
		console.log('prepareFiles ~ files:', filesToupload);

		for (let i = 0; i < filesToupload.length; i++) {
			formData.append(`file${i}`, filesToupload[i]);
		}

		return formData;
	};

	const testBee = async () => {
		let formData = await prepareFiles();
		console.log('testBee ~ prepareFiles:', formData);

		console.log('file0', formData.get('file0'));
		console.log('file1', formData.get('file1'));

		var headers = new Headers();
		headers.append('swarm-postage-batch-id', nodeBatchId18depth2days);
		headers.append('swarm-pin', 'true');
		headers.append('swarm-collection', 'true');
		headers.append('Content-Length', `${filesSize}`);

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
			.catch((error) => console.error('Error:', error));
	};
</script>

<section>
	<button class="btn btn-topup" on:click={testBee}>Stewardship</button>
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