Create OAUTH Token with Microsoft Azure

To create an OAuth token for a PHP script using Microsoft Azure to send emails, follow these steps:

1. **Register an Application in Azure AD:**
   - Go to the Azure Portal and navigate to the Azure Active Directory (Azure AD) admin center.
   - Register a new application and configure it to enable OAuth 2.0 client credentials flow.
   - Set the redirect URI to a valid endpoint if required by your setup.

2. **Configure Application Permissions:**
   - Ensure your application has the necessary permissions to send emails. You can add the `Mail.Send` permission under the Microsoft Graph API or Exchange Online API.
   - Grant admin consent for the application permissions.

3. **Generate OAuth Token:**
   - Use the following PHP code to generate an OAuth token using the client credentials flow:

```php
<?php
$clientId = 'YOUR_CLIENT_ID';
$clientSecret = 'YOUR_CLIENT_SECRET';
$tenantId = 'YOUR_TENANT_ID';
$tokenEndpoint = "https://login.microsoftonline.com/$tenantId/oauth2/v2.0/token";

$curl = curl_init();
curl_setopt_array($curl, [
    CURLOPT_URL => $tokenEndpoint,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => [
        'client_id' => $clientId,
        'client_secret' => $clientSecret,
        'grant_type' => 'client_credentials',
        'scope' => 'https://graph.microsoft.com/.default'
    ]
]);

$response = curl_exec($curl);
curl_close($curl);

$token = json_decode($response, true);
$accessToken = $token['access_token'];
?>
```

4. **Use the Token to Send Emails:**
   - Use the `$accessToken` to authenticate your PHPMailer or any other SMTP client to send emails. Here is an example using PHPMailer:

```php
<?php
require 'vendor/autoload.php';

use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

$mail = new PHPMailer(true);

try {
    //Server settings
    $mail->isSMTP();
    $mail->Host = 'smtp.office365.com';
    $mail->SMTPAuth = true;
    $mail->Username = 'YOUR_EMAIL_ADDRESS';
    $mail->Password = $accessToken; // Use the access token as the password
    $mail->SMTPSecure = 'tls';
    $mail->Port = 587;

    //Recipients
    $mail->setFrom('from@example.com', 'Mailer');
    $mail->addAddress('johndoe@example.com', 'John Doe');     // Add a recipient

    //Content
    $mail->isHTML(true);
    $mail->Subject = 'Here is the subject';
    $mail->Body    = 'This is the HTML message body <b>in bold!</b>';
    $mail->AltBody = 'This is the body in plain text for non-HTML mail clients';

    $mail->send();
    echo 'Message has been sent';
} catch (Exception $e) {
    echo "Message could not be sent. Mailer Error: {$mail->ErrorInfo}";
}
?>
```

Ensure that you replace placeholders like `YOUR_CLIENT_ID`, `YOUR_CLIENT_SECRET`, `YOUR_TENANT_ID`, and `YOUR_EMAIL_ADDRESS` with actual values from your Azure AD application registration.

Note: As of the current information, the client credentials flow is not directly supported for SMTP authentication in Azure. You may need to use the Graph API or other methods to send emails if SMTP with OAuth is required.