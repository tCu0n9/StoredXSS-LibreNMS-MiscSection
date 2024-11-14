# StoredXSS-LibreNMS-MiscSection


**Description:**


Stored XSS on the list parameters (Replace $DEVICE_ID with your specific $DEVICE_ID value):
`ajax_form.php` -> param: state



of Librenms version 24.10.1 ([https://github.com/librenms/librenms](https://github.com/librenms/librenms)) allows remote attackers to inject malicious scripts. When a user views or interacts with the page displaying the data, the malicious script executes immediately, leading to potential unauthorized actions or data exposure.


The vulnerability in the line:
```php
$attrib_val = get_dev_attrib($device, $name);
```
within the `dynamic_override_config` function arises because the value of `$attrib_val is` retrieved from untrusted data without any sanitization or encoding (at [Line 778](https://github.com/librenms/librenms/blob/master/includes/html/functions.inc.php#L778)). 

When `dynamic_override_config` is called, the unescaped `$attrib_val` is injected directly into the HTML (at [misc.inc.php](https://github.com/librenms/librenms/blob/master/includes/html/pages/device/edit/misc.inc.php)).


**Proof of Concept:**
1. Add a new device through the LibreNMS interface.
2. Edit the newly created device and select the Misc section.
3. In any of the following fields: "Override default ssh port", "Override default telnet port", "Override default http port" or "Unix agent port", enter the payload: `"><img src onerror="alert(document.cookie)">`.
4. Save the changes.
5. Observe that when the page loads, the XSS payload executes, triggering a popup that displays the current cookies.



![image](https://github.com/user-attachments/assets/097d17cb-7a6c-4924-add8-f867df643025)
![image](https://github.com/user-attachments/assets/8213d55a-d87a-4a6e-94bf-092877398da5)


**Impact:**

Execution of Malicious Code
