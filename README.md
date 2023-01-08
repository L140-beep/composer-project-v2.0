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
## 8. Используется интеграция со службой доставки для расчета стоимости (например, PickPoint или Почта России)
### sch-group/pickpoint
```php
$config = [
 'host' => '',
 'login' => '',
 'password' => '',
 'ikn' => '',
];    

$pickPointConf = new PickPointConf($config['host'], $config['login'], $config['password'], $config['ikn']);

$defaultPackageSize = new PackageSize(20, 20,20); // может быть null

$senderDestination = new SenderDestination('Москва', 'Московская обл.'); // Адрес отправителя

$client = new PickPointConnector($pickPointConf, $senderDestination, $defaultPackageSize);
 
 
Так же можно добавить кэширование, для ускорения запроса на авторизацию 
$redisCacheConf = [
 'host' => '127.0.0.1',
 'port' => 6379
];

$client = new PickPointConnector($pickPointConf, $senderDestination, $defaultPackageSize, $redisCacheConf);
```
### lapaygroup/russianpost
```php
<?php
try {
  $objectId = 2020; // Письмо с объявленной ценностью
  // Минимальный набор параметров для расчета стоимости отправления
  $params = [
              'weight' => 20, // Вес в граммах
              'sumoc' => 10000, // Сумма объявленной ценности в копейках
              'from' => 109012 // Почтовый индекс места отправления
              ];

  // Список ID дополнительных услуг 
  // 2 - Заказное уведомление о вручении 
  // 21 - СМС-уведомление о вручении
  $services = [2,21];

  $TariffCalculation = new \LapayGroup\RussianPost\TariffCalculation();
  $calcInfo = $TariffCalculation->calculate($objectId, $params, $services);
}

catch (\LapayGroup\RussianPost\Exceptions\RussianPostTarrificatorException $e) {
    // Обработка ошибок тарификатора 
    $errors = $e->getErrors(); // Массив вида [['msg' => 'текст ошибки', 'code' => код ошибки]]
}

catch (\LapayGroup\RussianPost\Exceptions\RussianPostException $e) {
    // Обработка ошибочного ответа от API ПРФ
}

catch (\Exception $e) {
    // Обработка нештатной ситуации
}

?>
```
## 9. Используется интеграция с социальными сетями для авторизации пользователей.
### hybridauth/hybridauth
```php
$config = [
    'callback' => 'https://example.com/path/to/script.php',
    'keys' => [
        'key' => 'your-twitter-consumer-key',
        'secret' => 'your-twitter-consumer-secret',
    ],
];

try {
    $twitter = new Hybridauth\Provider\Twitter($config);

    $twitter->authenticate();

    $accessToken = $twitter->getAccessToken();
    $userProfile = $twitter->getUserProfile();
    $apiResponse = $twitter->apiRequest('statuses/home_timeline.json');
}
catch (\Exception $e) {
    echo 'Oops, we ran into an issue! ' . $e->getMessage();
}
```

## 10. Данные о товарах регулярно отправляются в Яндекс.Маркет.
### yandex-market/yandex-market-php-partner
```php
// Указываем авторизационные данные
$clientId = '9876543210fedcbaabcdef0123456789';
$token = '01234567-89ab-cdef-fedc-ba9876543210';

// Создаем экземпляр клиента с базовыми методами
$baseClient = new \Yandex\Market\Partner\Clients\BaseClient($clientId, $token);

// Магазины возвращаются постранично
$pageNumber = 0;
do {
    $pageNumber++;
    
    // Получаем страницу магазинов с номером pageNumber
    $campaignsObject = $baseClient->getCampaigns(['page' => $pageNumber,]);
    // Получаем итератор по магазинам на странице
    $campaignsPage = $campaignsObject->getCampaigns();

    // Получаем количество магазинов на странице
    $campaignsCount = $campaignsPage->count();

    // Получаем первый магазин
    $campaign = $campaignsPage->current();
    // Печатаем идентификатор и URL магазина, затем переходим к следующему    
    for ($i = 0; $i < $campaignsCount; $i++) {
        echo 'ID: ' . $campaign->getId();
        echo 'Domain: ' . $campaign->getDomain();        
        $campaign = $campaignsPage->next();
    }
    
    // Получаем информацию о страницах. Возвращаемое количество страниц может увеличиваться 
    // по мере увеличения номера страницы. Последняя страница будет достигнута, 
    // когда вернется количество страниц, равное номеру текущей страницы    
    $campaignsTotalPages = $campaignsObject->getPager()->getPagesCount();
} while ($pageNumber != $campaignsTotalPages);    
```
## 11. Принимается онлайн-оплата от покупателей.
### stripe/stripe-php
```php
    $stripe = new \Stripe\StripeClient('sk_test_BQokikJOvBiI2HlWgH4olfQ2');
    $customer = $stripe->customers->create([
        'description' => 'example customer',
        'email' => 'email@example.com',
        'payment_method' => 'pm_card_visa',
    ]);
    echo $customer;
```
## 12. Применяются средства тестирования (например, PHPUnit).
### phpunit/phpunit
```php
<?php declare(strict_types=1);
use PHPUnit\Framework\TestCase;

final class StackTest extends TestCase
{
    public function testPushAndPop(): void
    {
        $stack = [];
        $this->assertSame(0, count($stack));

        array_push($stack, 'foo');
        $this->assertSame('foo', $stack[count($stack)-1]);
        $this->assertSame(1, count($stack));

        $this->assertSame('foo', array_pop($stack));
        $this->assertSame(0, count($stack));
    }
}
```
