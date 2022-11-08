# Console Command
(Laravel-Symfony)

## Docs
Symfony/Console là một package độc lậ được phải triển bởi Fabien Potencier , người đứng sau Symfony2 Framework , đương nhiên Symfony/Console cũng là một phần của Symfony2 Framework
```sh
    php bin/console make:command NameCommand 
```

Register command use AsCommand
```sh
#[AsCommand(
    name: 'build:app',
    description: 'Add a short description for your command',
)]
```
Run command
```sh
    php bin/console build:app 
```

### configure()
Ghi đè phương thức configure() để thực hiện thêm tham số cho command ,...

### $output->writeln();
In ouput


## Console input
### addArgument()
> $name Tên , $mode Dạng argument , $description mô tả , $default Mặc định ,
> $mode REQUIRED , OPTIONAL , IS_ARRAY
### addOption()
> $name Tên , $shortcut dadnjg short cut của tên , $mode Dạng option , $description mô tả  ,  $default Mặc định
> $mode VALUE_NONE , VALUE_REQUIRED , VALUE_OPTIONAL , VALUE_IS_ARRAY , VALUE_NEGATABLE

## Sử dụng dịch vụ container
```php
public function __construct(UserManager $userManager)
    {
        $this->userManager = $userManager;

        parent::__construct();
    }
```

## Lifecycle

`initialize(InputInterface $input, OutputInterface $output)` → được thực thi trước  interact và execute để khởi tạo các biến cần thiết

`interact(InputInterface $input, OutputInterface $output)` → được khởi tạo sau initialize và trước execute để nhằm kiểm tra các đối số , tham số bị thiếu và yêu cầu người dùng nhập lại

`execute(InputInterface $input, OutputInterface $output)` → được thực thi sau cùng  để thực thi và chạy các phần chính , logic của command trả về một command status

## Nhập liệu command 

```php
Call helper $helper = $this->getHelper('question');
```
### Xác nhận 
```php
use Symfony\Component\Console\Question\ConfirmationQuestion;

$question = new ConfirmationQuestion('Bạn có chắc chắn (y,n)?', true);
if(!$helper->ask($input,$output,$question))
{
    $io->infog("No Done ");
}
$io->success("Done");

```

### Câu hỏi 
```php
use Symfony\Component\Console\Question\Question;

$question = new Question('Tên của package của bạn ?', 'Giá trị mặc định ');

$bundleName = $helper->ask($input, $output, $question);
$io->writeln($bundleName);

```

### Chọn đáp án 
```php
use Symfony\Component\Console\Question\ChoiceQuestion;

$question = new ChoiceQuestion(
    'Chọn một trong các tùy chọn sau ( mặc định 0 )',
    ['red', 'blue', 'yellow'],
    0
);
$question->setErrorMessage('Tùy chọn %s không phù hợp vui lòng chọn lại .');

$color = $helper->ask($input, $output, $question);
$output->writeln('Tùy chọn của bạn : '.$color);

```

### Chọn nhiều đáp án
```php
use Symfony\Component\Console\Question\ChoiceQuestion;

$question = new ChoiceQuestion(
    'Chọn một hoặc nhiều trong các tùy chọn sau ( mặc định 0 )',
    ['red', 'blue', 'yellow'],
    0
);
$question->setMultiselect(true);
$question->setErrorMessage('Tùy chọn %s không phù hợp vui lòng chọn lại .');

$color = $helper->ask($input, $output, $question);
$output->writeln('Tùy chọn của bạn : '.$color);

```

### Tự động hoàn thành , gợi ý 
```php
Quesstion 
$bundles = ['AcmeDemoBundle', 'AcmeBlogBundle', 'AcmeStoreBundle'];
$callback = functon ($value) 
{
    return $value ;
}
$question->setAutocompleterValues($bundles);
$question->setAutocompleterCallback($callback);

```

### Loại bỏ khoảng trắng 
```php
Quesstion 
$question->setTrimmable(true);

```

### Trả lời nhiều dòng 
```php
Quesstion 
$question->setMultiline(true); 
```

### Ẩn đi các dữ liệu như password 
```php
Quesstion 
$question->setHidden(true);
$question->setHiddenFallback(true);
```

### Chuẩn hóa dữ liệu đầu vào 
```php
Quesstion 
$question->setNormalizer(function ($value) {
    return $value. 'asasasas';
});
```

### Validate dữ liệu đầu vào 
```php
Quesstion 
$question->setValidator(function ($answer) {
    if ($answer == "Ok") {
        throw new \RuntimeException(
            'The name of the bundle should be suffixed with \'Bundle\''
        );
    }

    return $answer;
});
// Số lần thực hiện lại 
$question->setMaxAttempts(1);
```

## Định dạng 
Gọi $formatter chung 
```sh 
$formatter = $this->getHelper('formatter');
```
### Định dạng một phần 
```php
formattedLine = $formatter->formatSection(
        'Thành công ',
        'Đã hoàn thành bản ghi '
);
$output->writeln($formattedLine);
```

### Định dạng một block 
```php
$errorMessages = ['Error!', 'Something went wrong'];
$formattedBlock = $formatter->formatBlock($errorMessages, 'error');
$output->writeln($formattedBlock);
```

### Định dạng ngắn một đoạn text 
```php
$message = "This is a very long message, which should be truncated";
$truncatedMessage = $formatter->truncate($message, 7);
$output->writeln($truncatedMessage);
```

## Thanh tiến trình 
>Khởi tạo một progress bar
```php
use Symfony\Component\Console\Helper\ProgressBar;

$progressBar = new ProgressBar($output, $Maxstep);

```

>Bắt đầu một progress bar
```php 
// Tăng max step 
$progressBar->setMaxSteps(200);
$progressBar->start();
$i = 0;
while ($i++ < 20) {
    sleep(1);
    $progressBar->advance(1);
}

$progressBar->finish();

```

>Custom một progress bar
```php 
// Tăng max step 
ProgressBar::setFormatDefinition('minimal',
    'Progress: %percent%% Memory : %memory% Max : %max% Bar : %bar% Step : %current% Time : %remaining%'
);
 $progressBar->setFormat('minimal');
 
 $progressBar->setFormat('Progress: %percent%% Memory');


// Cus tom thanh taskbar 
$progressBar->setBarCharacter('<comment><3333</comment>');

// the unfinished part of the bar
$progressBar->setEmptyBarCharacter('o');

// the progress character
$progressBar->setProgressCharacter('|wiiii');

// the bar width
$progressBar->setBarWidth(20);
```


>Config thời  gian 
```php  
        $progressBar->setRedrawFrequency(100);
        $progressBar->maxSecondsBetweenRedraws(0.2);
        $progressBar->minSecondsBetweenRedraws(0.1);
```
>Set message 
```php  
        $progressBar->setMessage('Message new ');
```

## Bảng 

>Tạo một bảng 
```php  
$table = new Table($output);
$table
    ->setHeaders(['ISBN', 'Title', 'Author'])
    ->setRows([
        ['99921-58-10-7', 'Divine Comedy', 'Dante Alighieri'],
        ['9971-5-0210-0', 'A Tale of Two Cities', 'Charles Dickens'],
        ['960-425-059-0', 'The Lord of the Rings', 'J. R. R. Tolkien'],
        ['80-902734-1-6', 'And Then There Were None', 'Agatha Christie'],
    ])
;

// Hiển thị 1 header 
$table->setHeaderTitle('Books');

//Hiển thị 1 footer 
$table->setFooterTitle('Page 1/2');

// Set chiều dài cột 
$table->setColumnWidths([10, 0, 30]);
// Set chiều dài trong của từng index column  
$table->setColumnWidth(2, 30);
// Set max with của từng index column 
$table->setColumnMaxWidth(0, 5);

// Thay đổi định dạng table 
$table->setVertical();

// Thay đổi style table 
$table->setStyle('default');

// Thêm một row mới 
$table->addRow(['Love']);
$table->render();
```

> Custom một bảng 
```php  
use Symfony\Component\Console\Helper\TableCell;
use Symfony\Component\Console\Helper\TableCellStyle;

$table->setRows([
    [
        '978-0804169127',
        new TableCell(
            'Divine Comedy',
            [
                'style' => new TableCellStyle([
                    'align' => 'center',
                    'fg' => 'red',
                    'bg' => 'green',

                    // or
                    'cellFormat' => '<info>%s</info>',
                ])
            ]
        )
    ],
]);
```
> Custom một bảng có nhiều row trong một row 
```php  
use Symfony\Component\Console\Helper\TableCell;
use Symfony\Component\Console\Helper\TableCellStyle;
use Symfony\Component\Console\Helper\TableSeparator;

$table
    ->setHeaders(['ISBN', 'Title', 'Author'])
    ->setRows([
        ['99921-58-10-7', 'Divine Comedy', 'Dante Alighieri'],
        new TableSeparator(),
        [new TableCell('This value spans 3 columns.', ['colspan' => 3])],
    ])
;
```
