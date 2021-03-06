## Snappy PDF/Image Wrapper for Laravel 5 and Lumen 5.1

### For Laravel 4.x, check [version 0.1](https://github.com/barryvdh/laravel-snappy/tree/0.1)

This package is a ServiceProvider for Snappy: [https://github.com/KnpLabs/snappy](https://github.com/KnpLabs/snappy).

### Wkhtmltopdf Installation

Choose one of the following options to install wkhtmltopdf/wkhtmltoimage.

1. Download wkhtmltopdf from [here](http://wkhtmltopdf.org/downloads.html); 
2. Or install as a composer dependency. See [wkhtmltopdf binary as composer dependencies](https://github.com/KnpLabs/snappy#wkhtmltopdf-binary-as-composer-dependencies) for more information.

#### Attention! Please note that some dependencies (libXrender for example) may not be present on your system and may require manual installation. 

### Testing the wkhtmltopdf installation

After installing you should be able to run wkhtmltopdf from the command line / shell.

If you went for the second option the binaries will be at `/vendor/h4cc/wkhtmltoimage-amd64/bin` and `/vendor/h4cc/wkhtmltopdf-amd64/bin`. 

#### Attention vagrant users!

Move the binaries to a path that is not in a synced folder, for example:

    cp vendor/h4cc/wkhtmltoimage-amd64/bin/wkhtmltoimage-amd64 /usr/local/bin/
    cp vendor/h4cc/wkhtmltopdf-amd64/bin/wkhtmltopdf-amd64 /usr/local/bin/

and make it executable:

    chmod +x /usr/local/bin/wkhtmltoimage-amd64 
    chmod +x /usr/local/bin/wkhtmltopdf-amd64

This will prevent the error 126.

### Package Installation

Require this package in your composer.json and update composer.

    composer require barryvdh/laravel-snappy

### Laravel

After updating composer, add the ServiceProvider to the providers array in config/app.php

    Barryvdh\Snappy\ServiceProvider::class,

Optionally you can use the Facade for shorter code. Add this to your facades:

    'PDF' => Barryvdh\Snappy\Facades\SnappyPdf::class,
    'SnappyImage' => Barryvdh\Snappy\Facades\SnappyImage::class,

Finally you can publish the config file:

    php artisan vendor:publish --provider="Barryvdh\Snappy\ServiceProvider"

### Snappy config file

The main change to this config file (config/snappy.php) will be the path to the binaries.

For example, when loaded with composer, the line should look like:

    'binary' => base_path('vendor/h4cc/wkhtmltopdf-amd64/bin/wkhtmltopdf-amd64'),
    
If you followed the vagrant steps, the line should look like:

    'binary'  => '/usr/local/bin/wkhtmltopdf-amd64',

### Lumen
In `bootstrap/app.php` add:
   
    class_alias('Barryvdh\Snappy\Facades\SnappyPdf', 'PDF');
    $app->register(Barryvdh\Snappy\LumenServiceProvider::class);

Optionally, add the facades like so:

    class_alias(Barryvdh\Snappy\Facades\SnappyPdf::class, 'PDF');
    class_alias(Barryvdh\Snappy\Facades\SnappyImage::class, 'SnappyImage');

To customise the configuration file, copy the file `/vendor/barryvdh/laravel-snappy/config/snappy.php` to the `/config` folder.

### Usage

You can create a new Snappy PDF/Image instance and load a HTML string, file or view name. You can save it to a file, or inline (show in browser) or download.

Using the App container:

    $snappy = App::make('snappy.pdf');
    //To file
    $html = '<h1>Bill</h1><p>You owe me money, dude.</p>';
    $snappy->generateFromHtml($html, '/tmp/bill-123.pdf');
    $snappy->generate('http://www.github.com', '/tmp/github.pdf');
    //Or output:
    return new Response(
        $snappy->getOutputFromHtml($html),
        200,
        array(
            'Content-Type'          => 'application/pdf',
            'Content-Disposition'   => 'attachment; filename="file.pdf"'
        )
    );

Using the wrapper:

    $pdf = App::make('snappy.pdf.wrapper');
    $pdf->loadHTML('<h1>Test</h1>');
    return $pdf->inline();

Or use the facade:

    $pdf = PDF::loadView('pdf.invoice', $data);
    return $pdf->download('invoice.pdf');

You can chain the methods:

    return PDF::loadFile('http://www.github.com')->inline('github.pdf');

You can change the orientation and paper size

    PDF::loadHTML($html)->setPaper('a4')->setOrientation('landscape')->setOption('margin-bottom', 0)->save('myfile.pdf')

If you need the output as a string, you can get the rendered PDF with the output() function, so you can save/output it yourself.

See the [wkhtmltopdf manual](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt) for more information/settings.

### License

This Snappy Wrapper for Laravel is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)
