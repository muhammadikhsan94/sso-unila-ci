# SSO-UNILA-CI

Library PHP-CI untuk memudahkan aplikasi menggunakan fasilitas login SSO Universitas Lampung.

## Instalasi Manual
1. Download <a href="https://github.com/apereo/phpCAS/releases">phpCAS</a> terbaru dan letakkan di path yang anda inginkan, contoh:

        /application/libraries/phpCAS-1.4.0

2. Salin *libraries/Cas.php* kedalam folder *libraries* aplikasi anda:

3. Salin *config/cas.php* kedalam folder *config* anda, dan lakukan perubahan:

        <?php  if ( ! defined('BASEPATH')) exit('No direct script access allowed');
        $config['cas_server_url'] = 'https://login.unila.ac.id/cas';
        $config['phpcas_path'] = '/application/libraries/phpCAS-1.4.0';
        $config['cas_disable_server_validation'] = TRUE;
        // $config['cas_debug'] = TRUE; // <--  use this to enable phpCAS debug mode

## Penggunaan
Sekarang, anda dapat mencoba dengan menambahkan di *controller* sebagai berikut:

        function index() {
            $this->load->library('cas');
            $this->cas->force_auth();
            $user = $this->cas->user();
            echo "Hello, $user->userlogin!";
        }