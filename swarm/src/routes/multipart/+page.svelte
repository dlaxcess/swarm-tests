<script lang="ts">
	const batchId = '241856bcc2267a69e13de19112aee051191b5afd3b579afcc8923d532994dd17';
	const urlImage =
		'https://ipfs.io/ipfs/bafkreia756ojs2x7ld7m6jjlz7djojgonh4sgnqotxsiorhux3az6v45ly';
	const urlText =
		'https://ipfs.io/ipfs/bafkreieivwe2vhxx72iqbjibxabk5net4ah5lo3khekt6ojyn7cucek624';
	const url = 'http://bee.swarm.public.dappnode:1633/bzz';

	function convertUint8ArrayToBinaryString(u8Array: Uint8Array): String {
		const len = u8Array.length;
		let bin = '';

		for (let i = 0; i < len; i++) bin += String.fromCharCode(u8Array[i]);

		return bin;
	}

	const testBee = async () => {
		const text = await (await fetch(urlText)).text();

		const blobImage = await (await fetch(urlImage)).blob();
		const uint8ArrayImage = new Uint8Array(await blobImage.arrayBuffer());
		const binaryImage = convertUint8ArrayToBinaryString(uint8ArrayImage);

		let boundary = `TESTBEEJS${Math.random().toString().substr(8)}`;
		let body = '';

		// TEXT
		body += `--${boundary}\r\n`;
		body += 'Content-Disposition: form-data; name="part0"\r\n';
		body += `Content-Type: text/plain\r\n`;
		body += `Content-Length: ${text.length}\r\n`;
		body += '\r\n';
		body += text;
		body += '\r\n';

		// IMAGE
		body += `--${boundary}\r\n`;
		body += 'Content-Disposition: form-data; name="part1"\r\n';
		body += `Content-Type: ${blobImage.type}\r\n`;
		body += `Content-Length: ${blobImage.size}\r\n`;
		body += '\r\n';
		body += binaryImage;
		body += '\r\n';

		// END
		body += `--${boundary}--`;

		console.log(body);
		console.log(body.length);

		let headers = new Headers();
		headers.append('Content-Type', `multipart/form-data; boundary=${boundary}`);
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
