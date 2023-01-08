# composer-project-v2.0
## 1. Используется БД для хранения данных.
### doctrine/dbal
```php
<?php
use Doctrine\DBAL\DriverManager;

$conn = DriverManager::getConnection($params, $config);

$sql = "SELECT * FROM articles";
$stmt = $conn->query($sql);
```

## 2. Используется кеш в памяти для временного хранения сложных запросов к БД.
### laragear/cache-query
```php
use Illuminate\Support\Facades\DB;
use App\Models\Article;

DB::table('articles')->latest('published_at')->take(10)->cache()->get();

Article::latest('published_at')->take(10)->cache()->get();
```
## 3. Формируются XLS-отчеты на основе данных.
### phpoffice/phpspreadsheet
```php
<?php
use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

$spreadsheet = new Spreadsheet();
$sheet = $spreadsheet->getActiveSheet();
$sheet->setCellValue('A1', 'Hello World !');

$writer = new Xlsx($spreadsheet);
$writer->save('hello world.xlsx');
```
## 4. Формируются PDF-документы на основе данных.
### tecnickcom/tcpdf
```php
<?php
require __DIR__ . '/vendor/autoload.php';
$pdf = new TCPDF();                 // create TCPDF object with default constructor args
$pdf->AddPage();                    // pretty self-explanatory
$pdf->Write(1, 'Hello world');      // 1 is line height

$pdf->Output('hello_world.pdf');    // send the file inline to the browser (default).

?>
```
## 5. Отправляются SMS-сообщения для верификации пользователей.
### twilio/sdk
```php
// Send an SMS using Twilio's REST API and PHP
<?php
$sid = "ACXXXXXX"; // Your Account SID from www.twilio.com/console
$token = "YYYYYY"; // Your Auth Token from www.twilio.com/console

$client = new Twilio\Rest\Client($sid, $token);
$message = $client->messages->create(
  '8881231234', // Text this number
  [
    'from' => '9991231234', // From a valid Twilio number
    'body' => 'Hello from Twilio!'
  ]
);

print $message->sid;
```
## 6. Отправляются E-mail-уведомления и рассылки для пользователей.
### symfony/mailer
```php
use Symfony\Component\Mailer\Transport;
use Symfony\Component\Mailer\Mailer;
use Symfony\Component\Mime\Email;

$transport = Transport::fromDsn('smtp://localhost');
$mailer = new Mailer($transport);

$email = (new Email())
    ->from('hello@example.com')
    ->to('you@example.com')
    //->cc('cc@example.com')
    //->bcc('bcc@example.com')
    //->replyTo('fabien@example.com')
    //->priority(Email::PRIORITY_HIGH)
    ->subject('Time for Symfony Mailer!')
    ->text('Sending emails is fun again!')
    ->html('<p>See Twig integration for better HTML integration!</p>');

$mailer->send($email);
```
## 7. Используется облачное хранилище AWS S3 или Windows Azure Blob для статичных файлов.
### microsoft/azure-storage-common
```php
 # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');

    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);

    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();

    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

```php
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";

    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```
