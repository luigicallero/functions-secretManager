## Using DON-Hosted Secrets in Requests

This section walks through using DON (Decentralized Oracle Network)-hosted secrets when making requests in Chainlink Functions. The process follows the official [Chainlink documentation](https://docs.chain.link/chainlink-functions/tutorials/api-use-secrets).

### Prerequisites

To use this functionality, ensure you have the following setup in your environment:
- **Ethereum Sepolia RPC URL**: Set as the environment variable `ETHEREUM_SEPOLIA_RPC_URL`.
- **Private Key**: Set as the environment variable `PRIVATE_KEY`, which will be used to sign transactions.
- **API Key for Secrets**: For example, an Alpaca API key can be provided as the environment variable `ALPACA_API_KEY`.
- **Subscription ID**: You must have a valid Chainlink Functions subscription ID on Sepolia.

Ensure these variables are properly set up in a `.env` file.

### Code Explanation

This example demonstrates uploading secrets to the DON and making a request to a Chainlink Functions consumer contract deployed on Ethereum Sepolia.

```javascript
const secrets = { apiKey: process.env.ALPACA_API_KEY };
const slotIdNumber = 0; // slot ID where to upload the secrets
const expirationTimeMinutes = 15; // expiration time in minutes of the secrets

const routerAddress = "0xb83E47C2bC239B3bf370bc41e1459A34b41238D0"; // FunctionsRouter address for Sepolia
const donId = "fun-ethereum-sepolia-1"; // DON ID for Sepolia
const gatewayUrls = [
  "https://01.functions-gateway.testnet.chain.link/",
  "https://02.functions-gateway.testnet.chain.link/"
];
```

The script first encrypts the secrets using the Chainlink toolkit and then uploads them to the DON for secure use during request execution.

```javascript
const encryptedSecretsObj = await secretsManager.encryptSecrets(secrets);
const uploadResult = await secretsManager.uploadEncryptedSecretsToDON({
  encryptedSecretsHexstring: encryptedSecretsObj.encryptedSecrets,
  gatewayUrls: gatewayUrls,
  slotId: slotIdNumber,
  minutesUntilExpiration: expirationTimeMinutes,
});
```

### Error Encountered

While executing the script, the following error occurs:

```
Error: sender not allowlisted
```

This error occurs when trying to upload encrypted secrets to the DON gateways. The full error output is as follows:

```bash
Uploading secret to DON...
Upload encrypted secret to gateways https://01.functions-gateway.testnet.chain.link/,https://02.functions-gateway.testnet.chain.link/. slotId 0. Expiration in minutes: 15
Error encountered when attempting to send request to DON gateway URL #1 of 2
https://01.functions-gateway.testnet.chain.link/:
{"jsonrpc":"2.0","id":"4133926951","error":{"code":-32600,"message":"sender not allowlisted"}}
Error encountered when attempting to send request to DON gateway URL #2 of 2
https://02.functions-gateway.testnet.chain.link/:
{"jsonrpc":"2.0","id":"3756256295","error":{"code":-32600,"message":"sender not allowlisted"}}
Error: Failed to send request to any of the DON gateway URLs:
["https://01.functions-gateway.testnet.chain.link/","https://02.functions-gateway.testnet.chain.link/"]
```

### Troubleshooting Steps

If you encounter the `sender not allowlisted` error, it indicates that the wallet address being used to upload the secrets is not allowlisted to use the Chainlink Functions DON gateways. Here are steps you can take to resolve the issue:

1. **Ensure Wallet Address Is Allowlisted**: To use DON-hosted secrets, the sender (your wallet address) must be allowlisted by Chainlink. You will need to contact the Chainlink team or check the documentation to get your address added to the allowlist for accessing DON gateways.

2. **Check the Correct Network**: Double-check that you're using the correct network and router address for Sepolia. Ensure that `ETHEREUM_SEPOLIA_RPC_URL` points to a valid Sepolia RPC endpoint and that the `routerAddress` and `donId` match the official Chainlink deployment for Sepolia.

3. **Update Dependencies**: Ensure that all necessary dependencies are installed and up-to-date. Specifically, the `@chainlink/functions-toolkit` package should be the latest version. You can update it by running:
   ```bash
   npm install @chainlink/functions-toolkit@latest
   ```

### Next Steps

Currently awaiting for Chainlink support to come back for a solution to the DON allow list issue: 
https://discord.com/channels/592041321326182401/1080516970765623437/1297074742712336415