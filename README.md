# Bitcoin-Stamp-Decoder
Bitcoin Stamp Decoder

This HTML and JavaScript code implements a **Bitcoin STAMP Decoder** web application that decodes Counterparty (XCP) and STAMP data from Bitcoin transactions. It fetches transaction data from the BlockCypher API, processes it, and displays Bitcoin and Counterparty-related information, including STAMP (Classic and Olga) data. Below is a summary of its functionality in bullet points:

- **User Interface**:
  - Provides a form to input a Bitcoin transaction ID (txid) for decoding.
  - Displays decoded data in a structured format with sections for Bitcoin and Counterparty data.
  - Includes links to view the transaction on external explorers (Stampchain.io, BlockCypher, Blockchain.com, Blockstream).
  - Responsive design with CSS styling, adapting to mobile screens (media queries for max-width: 600px).
  - Shows a notification ("Done! LFG") on successful decoding, which auto-dismisses after 3 seconds.
  - Includes a footer with a link to a blog post and a donation address placeholder.

- **Transaction Data Retrieval**:
  - Fetches transaction data from the BlockCypher API (`https://api.blockcypher.com/v1/btc/${network}/txs/${tx}`) using the provided txid.
  - Supports mainnet (`main`) and testnet (`test3`) based on the `network` URL parameter (defaults to `main` if invalid).
  - Validates the txid format (64-character hexadecimal) before fetching.
  - Handles pagination for transaction outputs by following `next_outputs` links until all outputs are retrieved.
  - Logs errors and displays them in the UI if the fetch fails or data is invalid.

- **Data Decoding**:
  - Processes Bitcoin transaction data to extract:
    - Transaction ID (hash), block height, block time, and sender address.
    - Input UTXO (previous transaction hash) and output values (e.g., first output in satoshis).
    - Script types (`pay-to-pubkey-hash`, `pay-to-multi-pubkey-hash`, `null-data`, `pay-to-witness-script-hash`) and associated scripts/addresses.
  - Decodes Counterparty data encoded in `null-data` (OP_RETURN) or `pay-to-multi-pubkey-hash` (multisig) outputs:
    - Uses RC4 decryption with the UTXO hash as the key to decode script data.
    - Checks for the `CNTRPRTY` prefix (hex: `434e545250525459`) to validate Counterparty messages.
    - Extracts message ID, asset ID, issuance quantity, divisibility, lock status, reset status, and subasset (if applicable).
  - Supports message IDs 20 (Issuance), 21 (Issuance with Subasset), and 22 (Issuance with potential STAMP data).

- **STAMP Decoding**:
  - Handles two STAMP formats: **Classic** (base64-encoded in description) and **Olga** (hex-encoded in multisig or P2WSH outputs).
  - For Classic STAMPs:
    - Extracts base64 data from descriptions starting with `stamp:`.
    - Identifies MIME types (GIF, PNG, JPEG, SVG+XML, or HTML) based on base64 prefixes.
    - Displays the image or HTML content in an `<img>` or `<iframe>` tag.
    - Calculates image dimensions (width x height) for images or SVGs; defaults to 300x300 for HTML.
  - For Olga STAMPs:
    - Extracts hex data from multisig or P2WSH outputs, including file size and content.
    - Converts hex to base64, validates, and identifies MIME type.
    - Displays file size, burned satoshis/BTC, MIME type, and dimensions, along with the image or HTML content.
  - Validates base64 and hex data, logging and displaying errors for invalid formats.

- **Helper Functions**:
  - `getImageDimensions`: Determines dimensions of images (GIF, PNG, JPEG, SVG) or defaults to 300x300 for HTML.
  - `xcp_rc4`: Performs RC4 decryption using a key (UTXO hash) on hex data.
  - `hex2a`, `hex2bin`, `bin2hex`: Convert between hex, binary, and ASCII for decoding.
  - `hexToDec`, `asset_name`, `hex_to_subasset`: Convert hex to decimal, generate asset names, and decode subasset names.
  - `wl`: Formats output as labeled rows for display.
  - `chunk`: Splits long strings (e.g., base64) into chunks for readability.
  - `output2`: Generates links to external blockchain explorers.

- **Error Handling**:
  - Validates txid, hex, and base64 formats, displaying errors in the UI if invalid.
  - Handles API fetch failures, invalid JSON, and decoding errors with console logging and user-facing error messages.
  - Checks for sufficient data length and valid file sizes in Olga STAMP decoding.

- **External Dependencies**:
  - Uses `sha256.js` for cryptographic functions (not utilized in the provided code).
  - Relies on the BlockCypher API for transaction data.
  - Uses browser APIs (`fetch`, `DOMParser`, `Image`) for data processing and rendering.

- **Limitations and Notes**:
  - Supports only specific MIME types (PNG, GIF, JPEG, SVG+XML, HTML) for STAMP data.
  - Includes a hardcoded list of example transaction IDs (`example_txs`) that is not used in the provided code.
  - Donation address is a placeholder (NSID Wallet TBD).

This code provides a robust tool for decoding and visualizing Bitcoin transaction data, with a focus on Counterparty and STAMP protocols, while ensuring error handling and a responsive UI.
