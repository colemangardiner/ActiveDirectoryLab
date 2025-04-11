<h1>Active Directory Home Lab</h1>


<h2>Description</h2>
Project shows the process of building a home lab using virutal machines that allows you to experiment with Active Directory. Using PowerShell script that walks the user through adding user clients and computers. Using an VM internal network and an external network to my personal computers IP (RAS/NAT). Using domain admin, DHCP, DNS, and 3 seperate NIC's
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Active Directory</b>
- <b>Domain Admin</b>

<h2>Environments Used </h2>

- <b>Windows 10 ISO (client)</b>
- <b>Server 19 ISO </b>
- <b>Oracle VirtualBox Virtual Machine </b>

<h2>Program walk-through:</h2>

<h2>Step 1: Install Oracle VirtualBox</h2>

- <b>Go to https://www.virtualbox.org and download the latest version.</b> 

- <b>Download the Windows Server 2019 ISO (choose the “ISO” format).</b>

- <b>Save the file locally—it will be mounted later to install the OS.</b>

<h2>Step 2: Download Windows Server 2019 ISO</h2>

- <b>Open VirtualBox → Click New.</b>

- <b>Configure the VM:

-Name: DC01 (or anything descriptive)

-Type: Microsoft Windows

-Version: Windows 2019 (64-bit).</b>

- <b>Set Memory Size: Allocate at least 2048 MB (2 GB) RAM, preferably 4 GB.</b>

- <b>Select Create a virtual hard disk now.</b>

- <b>Choose:

-VDI (VirtualBox Disk Image)

-Dynamically allocated

-Size: At least 40 GB recommended</b>

- <b>Once created, select the VM → Click Settings → Go to System tab:

-Remove Floppy from boot order.</b>

- <b>Under Storage:

-Click the empty optical drive.

-Choose the downloaded Windows Server ISO using “Choose a disk file…”</b>

<h2>Step 4: Install Windows Server on the VM</h2>

- <b>Start the VM; it should boot into the ISO.</b>

- <b>Proceed with the Windows installation:

-Choose Windows Server 2019 Standard (Desktop Experience).

-Follow prompts for region, language, etc.

-Set a local Administrator password.</b>

- <b>After reboot, log in with your admin password.</b>

- <b>Install VirtualBox Guest Additions if needed (for better display resolution and mouse capture).</b>

<h2>Step 5: Rename the Server and Set a Static IP (Recommended)</h2>

- <b>In Server Manager → Click on the computer name → Rename it to DC01 (or your choice).</b>

- <b>Restart the VM.</b>

- <b>Go to Network Settings → Set a static IP address for consistency in your lab.</b>

<h2>Step 6: Install Active Directory Domain Services (AD DS)</h2>

- <b>Open Server Manager → Click Add roles and features.</b>

- <b>Click through until you reach Server Roles.</b>

- <b>Select Active Directory Domain Services → Accept features → Next → Install.</b>

- <b>After installation:

-In Server Manager, click the yellow alert flag → Select Promote this server to a domain controller.

-Choose Add a new forest.

-Root domain name: e.g., lab.local

-Set a DSRM password (Directory Services Restore Mode).</b>

- <b>Complete the wizard and reboot when prompted.</b>

<h2>Step 7: Create Organizational Units and Add Users with PowerShell</h2>

- <b>Log in after reboot as the domain administrator.</b>

- <b>Open PowerShell as Administrator.</b>

- <b>Run the following script to create an OU and 10 users:</b>

<pre><code>$PASSWORD_FOR_USERS   = "Password1"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan

    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
</code></pre>

<h2>Step 11: Verify Users in Active Directory</h2>

- <b>Open Active Directory Users and Computers.</b>

- <b>Browse to the _USERS OU you created.</b>

- <b>You should see accounts like jsmith, jdoe, mjohnson, etc.</b>

- <b>You can double-click each user to confirm properties like name, login, and employee ID.</b>

<h2>Step 12: Log in as One of the Created Users</h2>

- <b>Create another VM (like Windows 10) and join it to your domain.</b>

- <b>Log in using one of the new accounts.</b>

- <b>Confirm that the domain login works properly.</b>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
