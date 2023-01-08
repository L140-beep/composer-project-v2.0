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
