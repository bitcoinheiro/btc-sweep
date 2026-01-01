# **Bitcoin Offline Sweeper**

This document provides the complete user guide and technical specifications for the Bitcoin Offline Sweeper tool. This utility is designed for high-security environments, allowing users to compose and sign simple Sweep Bitcoin transactions from WIF keys on air-gapped/offline hardware for future broadcasting.

## **1\. User Guide**

### **Phase 1: Online (Information Gathering)**

1. **Open the Tool**: Launch the HTML file on an internet-connected computer/smartphone.  
2. **Online Source Entry**: Enter the **Public Bitcoin Address** you wish to sweep.  
3. **Data Retrieval**: Click **Fetch Coins & Tx Data**. The tool will securely download UTXO (Unspent Transaction Output) data and raw transaction hexes via the Blockstream API.  
4. **Air-Gap Transition**: Save the current page (Ctrl+S or Cmd+S) to a portable USB drive and move the file to your offline, secure machine. If you only have one device, now would be advisable to disconnect from the internet with “Airplane Mode”. This is optional but encouraged.

### **Phase 2: Offline (Signing)**

1. **Secure Environment**: Ensure the computer is completely disconnected from all networks (WiFi/Bluetooth/Ethernet). This is optional but encouraged.
2. **Authentication**: Enter your **Private Key (WIF)**, the **Destination Address**, and your preferred **Fee Rate (sat/vB)**.  
3. **Cryptographic Signing**: Click **Sign Transaction**. The tool uses a local BitcoinJS engine to sign the inputs.  
4. **Verification**: Carefully review the details in the **HEX Transaction Details** section. Ensure the "Sent Amount" and "To Address" match your expectations before proceeding. It is also advisable to use another tool to preview the transaction and check all data before broadcasting. Mempool.space has a good <a href="https://mempool.space/tx/preview" target="_blank">Preview Transaction tool</a> before broadcast.

### **Phase 3: Broadcast**

1. **Export Hex**: Copy the generated **Signed Transaction Hex**.  
2. **Finalize**: Transfer this hex back to an online device and broadcast it using a trusted service (e.g., <a href="https://blockstream.info/tx/push" target="_blank">Blockstream.info</a>, <a href="https://mempool.space/tx/preview" target="_blank">Mempool.space</a>, or your own full node).

## **2\. Technical Security & PSBT Logic**

To prevent common transaction failures, this tool strictly adheres to the following standards:

* **PSBT Framework**: Utilizes Partially Signed Bitcoin Transactions to manage state.  
* **Input Integrity**: Implements nonWitnessUtxo requirements. By providing the full raw hex of the previous transaction, the signer can verify the input amounts and scripts independently, preventing "Fee Siphoning" attacks and script validation errors.  
* **Canonical Scripting**: The finalization logic ensures that scriptSig and witness fields are constructed correctly, avoiding issues like OP\_PUSHBYTES length mismatches or disabled opcode errors (OP\_MUL).

## **3\. Open Source Licenses**

* **BitcoinJS-Lib**: MIT License. Copyright (c) 2011-2020 BitcoinJS contributors.  
* **Tailwind CSS**: MIT License. Copyright (c) Tailwind Labs, Inc.  
* **Buffer**: MIT License. Copyright (c) Feross Aboukhadijeh.

## **4\. Disclaimer and Warranty**

**THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND**, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.

**IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE** FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**WARNING**: Private keys grant total control over your funds. Only use this tool in a trusted, malware-free environment. Always verify destination addresses twice.
