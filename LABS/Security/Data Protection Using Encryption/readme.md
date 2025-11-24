

## Prerequisites

- **AWS account:** You’ll need access with permissions to create and manage KMS keys and IAM roles.
- **IAM role or user:** Ensure you have sufficient permissions (KMS, IAM, and EC2/SSM if using a managed environment).
- **AWS CLI installed:** Version 2 recommended.
- **Python and pip:** Required to install the AWS Encryption CLI.

---

## Configure aws cli credentials

Set up your AWS CLI credentials and default region.

1. **Run the AWS configure command:**
   ```bash
   aws configure
   ```
   - **Access key ID:** Enter your AWS access key.
   - **Secret access key:** Enter your AWS secret.
   - **Default region:** e.g. us-west-2
   - **Output format:** Leave blank or set to json.

2. **Verify credentials file (optional):**
   ```bash
   cat ~/.aws/credentials
   ```

> Tip: Use temporary credentials with IAM roles when possible and rotate your keys regularly.

---

## Create an aws kms symmetric key

Create a symmetric KMS key configured for encrypt/decrypt operations. These steps mirror the AWS console flow.

1. **Choose key type:**
   - **Symmetric:** A single key used for encrypting and decrypting data.
2. **Select key usage:**
   - **Encrypt and decrypt:** Limit usage to encryption and decryption operations.
3. **Add labels:**
   - **Alias:** MyKMSKey  
   - **Description:** Key used to encrypt and decrypt data files
4. **Define key administrators (optional but recommended):**
   - **Admins:** Select the IAM roles/users that can administer the key (e.g., voclabs).
   - **Effect:** Adds them to the key policy under Sid “Allow administration of the key”.
5. **Define key users (optional but recommended):**
   - **Users:** Select IAM roles/users allowed to use the key in cryptographic operations (e.g., voclabs).
   - **Effect:** Adds them under Sids “Allow use of the key” and “Allow attachment of persistent resources”.
6. **Finish and note the key ARN:**
   - **Copy the Key ARN:** You’ll need it for the CLI wrapping-keys configuration.
   - **Example alias reference:** alias/MyKMSKey

> Tip: Keep the key alias memorable; use environment-specific aliases like alias/prod-data-key or alias/dev-labs-key.

---

## Install the aws encryption cli

Install the CLI and ensure your PATH is set correctly.

1. **Install via pip:**
   ```bash
   pip3 install aws-encryption-sdk-cli
   ```
2. **Export local bin to PATH (if needed):**
   ```bash
   export PATH=$PATH:/home/ssm-user/.local/bin
   ```
3. **Verify installation:**
   ```bash
   aws-encryption-cli --version
   ```

> Tip: If installing in a restricted environment, defaulting to user install is fine; just ensure your PATH includes ~/.local/bin.

---

## Encrypt plaintext

Encrypt a sample file using the AWS Encryption CLI and your KMS key.

1. **Create sample files (optional):**
   ```bash
   touch secret1.txt secret2.txt secret3.txt
   echo 'TOP SECRET !!!!' > secret1.txt
   cat secret1.txt
   ```
2. **Encrypt with key alias (or ARN):**
   ```bash
   aws-encryption-cli --encrypt \
     --input secret1.txt \
     --wrapping-keys key=alias/MyKMSKey \
     --metadata-output ~/metadata \
     --encryption-context purpose=test \
     --commitment-policy require-encrypt-require-decrypt \
     --output ~/output/
   ```
   - **wrapping-keys:** Use key=alias/MyKMSKey or key=<your-key-arn>.
   - **encryption-context:** Adds non-secret metadata for cryptographic integrity checks.
   - **commitment-policy:** Ensures secure algorithm commitments are enforced.
   - **output:** Directory where the encrypted file will be written (e.g., secret1.txt.encrypted).

3. **Confirm output:**
   ```bash
   ls -la ~/output/
   ```

> Tip: Encryption context is validated on decrypt; keep it consistent across operations for the same data flow.

---

## Decrypt ciphertext

Decrypt the encrypted file using the same key and encryption context.

1. **Set an environment variable for your key ARN (optional):**
   ```bash
   export KEY_ARN=<your-key-arn>
   ```
2. **Decrypt the file:**
   ```bash
   aws-encryption-cli --decrypt \
     --input ~/output/secret1.txt.encrypted \
     --wrapping-keys key=$KEY_ARN \
     --commitment-policy require-encrypt-require-decrypt \
     --encryption-context purpose=test \
     --metadata-output ./metadata \
     --max-encrypted-data-keys 1 \
     --buffer \
     --output .
   ```
   - **max-encrypted-data-keys:** Restricts the number of data keys for safety.
   - **buffer:** Streams to stdout unless output is specified; here we write to the current directory.
3. **Verify plaintext:**
   ```bash
   cat ./secret1.txt
   ```

> Tip: If decryption fails, check that the encryption context matches and that your IAM principal is listed as a key user in the key policy.

---

### Troubleshooting

- **AccessDeniedException:** Ensure your IAM role/user is in the KMS key’s policy as a key user and the resource policy permits your actions.
- **Key not found:** Confirm the alias or ARN is correct and in the same region as your CLI configuration.
- **PATH issues:** Add ~/.local/bin to PATH if aws-encryption-cli isn’t found after pip install.
- **Region mismatch:** KMS keys are regional; make sure aws configure region matches your key’s region.

---

### Cleanup

- **Remove test files:**
  ```bash
  rm -f secret1.txt secret2.txt secret3.txt ~/output/secret1.txt.encrypted
  ```
- **Review key rotation policies:** Consider enabling automatic rotation for long-lived keys.
- **Audit key usage:** Use CloudTrail and KMS metrics to monitor encrypt/decrypt calls.

---

### Notes

- **Security best practice:** Limit admin and usage permissions to the minimum necessary, and prefer IAM roles over long-lived access keys.
- **Context consistency:** Keep your encryption context stable across encrypt/decrypt to avoid integrity mismatches.
