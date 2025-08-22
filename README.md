# Managing Accounts with Group Policy

This tutorial shows how to manage Active Directory accounts by setting lockout policies, disabling and enabling accounts, and checking security logs on both the Domain Controller and a client.

---

## Environments and Technologies Used

- **Microsoft Azure**
- **Windows Server 2022** (Domain Controller – dc-1)
- **Windows 11** (Client machine – client-1)
- **Active Directory Domain Services (AD DS)**
- **Group Policy Management**
- **Event Viewer**

---

## Operating Systems Used

- **Windows Server 2022** – Domain Controller (**dc-1**)  
- **Windows 11** – Client machine (**client-1**)  

---

## Steps

### 1. Simulate an Account Lockout
1. Log into **client-1** with any **test domain account** you created earlier (e.g., `bap.buk`).
2. Enter an **incorrect password** 10 times.
   - *Why?* This simulates a brute-force attempt that triggers account lockout policies.
<p>
  <img width="864" height="460" alt="image" src="https://github.com/user-attachments/assets/70315ab0-af9b-4624-a74a-223556be29ef" />
  <img width="504" height="506" alt="image" src="https://github.com/user-attachments/assets/f0fdb239-4125-4408-900b-bb39d3abd63d" />
</p>
---

### 2. Configure Group Policy to Lock Accounts After 5 Attempts
1. Log into **dc-1** as `jane_admin`.
2. Open **Group Policy Management**:
   - `Server Manager → Tools → Group Policy Management`
3. Create or edit the **Default Domain Policy**.
<p>
  <img width="1502" height="1052" alt="image" src="https://github.com/user-attachments/assets/4a333c91-b938-4066-ae91-c2f404c52539" />
</p>
4. Navigate to:
  - Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy
  <p>
    <img width="1454" height="964" alt="image" src="https://github.com/user-attachments/assets/08cdbcdb-e863-4b7f-bcd8-f5a1d426f4eb" />
  </p>
5. Configure the following settings:
- **Account lockout threshold**: `5` invalid attempts  
- **Account lockout duration**: `30 minutes` (or preferred)  
- **Reset account lockout counter after**: `10 minutes`  
- *Why?* This limits the number of failed login attempts, protecting accounts from brute-force attacks.
<p>
  <img width="828" height="1010" alt="image" src="https://github.com/user-attachments/assets/c66c7864-3a7f-4a36-81c5-4e2da0664489" />
  <img width="828" height="1010" alt="image" src="https://github.com/user-attachments/assets/68fe42d5-93dd-4b90-8cf4-20c69c63ded2" />
</p>

6. Close Group Policy Management and **force update** on the client: gpupdate /force
<p>
  <img width="1084" height="524" alt="image" src="https://github.com/user-attachments/assets/4c00028b-d4a3-46aa-9167-991289087bda" />
</p>

---

### 3. Test Lockout Policy
1. On **client-1**, attempt to log into the same user account with a **bad password** 6 times.
  - Observe that the account is now **locked out**.
<p>
  <img width="868" height="462" alt="image" src="https://github.com/user-attachments/assets/046611a7-a475-418a-bc32-14fe00316d90" />
  <img width="496" height="620" alt="image" src="https://github.com/user-attachments/assets/5ba0ccf6-5fa7-48f0-9de1-b8367d7a471b" />
</p>
2. On **dc-1**, open **ADUC** and unlock:
- Right-click the locked-out account and select **Properties → Account → Unlock account**
<p>
  <img width="1110" height="1474" alt="image" src="https://github.com/user-attachments/assets/494306dd-2903-4929-89f8-3a20b1eafa6b" />
</p>
3. Attempt to log into client-1 again with the correct password.  
- Confirm that the account is usable again.
<p>
  <img width="1270" height="1446" alt="image" src="https://github.com/user-attachments/assets/f8cb66cf-2465-4f2e-85d0-953d1d95ec9e" />
</p>
---

### 4. Enabling and Disabling Accounts
1. In **ADUC**, right-click the same test account and choose:
- **Disable Account**
<p>
  <img width="1424" height="1562" alt="image" src="https://github.com/user-attachments/assets/7114904c-caa2-4e27-ba09-7b572586088a" />
</p>
2. Attempt to log into **client-1** with the disabled account.
- *Observation:* You’ll get a message that the account is disabled.
<p>
  <img width="510" height="534" alt="image" src="https://github.com/user-attachments/assets/e8decd82-e58c-46b7-bb58-73bbbe05604b" />
</p>
3. Go back to ADUC and **Enable Account**.
<p>
  <img width="1428" height="1484" alt="image" src="https://github.com/user-attachments/assets/d55be4e7-52d9-48de-8c04-32100578dec8" />
</p>
4. Log in again on client-1 to verify access works.
<p>
  <img width="454" height="114" alt="image" src="https://github.com/user-attachments/assets/197c02cd-8589-4f43-a010-c9bc3d10f908" />
</p>

---

### 4. Observing Logs (Event Viewer)
1. On **dc-1**:
- Open **Event Viewer** → `Windows Logs → Security`
- Look for events related to **failed login attempts**, **account lockouts**, and **account management actions**.
<p>
  <img width="2074" height="1362" alt="image" src="https://github.com/user-attachments/assets/37e87977-b2f2-463b-92ad-9c8e11733600" />
  <img width="2090" height="1646" alt="image" src="https://github.com/user-attachments/assets/be1d9aca-0d4d-4881-8090-aaae7ea293a4" />
</p>
2. On **client-1**:
- Open **Event Viewer** → `Windows Logs → Security`
- Look for failed logon attempts and lockout events triggered on the client side.
<p>
  <img width="2146" height="1622" alt="image" src="https://github.com/user-attachments/assets/46ce407c-9c88-4b6a-a104-95abb9949831" />
</p>
