# SSO-UNILA-CI

Library PHP-CI untuk memudahkan aplikasi menggunakan fasilitas login SSO Universitas Lampung.

## Instalasi Manual

1. Salin isi folder *libraries* kedalam folder *application/libraries* aplikasi anda:

2. Salin *config/cas.php* kedalam folder *config* anda, dan lakukan perubahan:

		<?php  if ( ! defined('BASEPATH')) exit('No direct script access allowed');
		$config['cas_server_url'] = 'https://login.unila.ac.id';
		$config['phpcas_path'] = '/application/libraries/phpCAS';
		$config['cas_disable_server_validation'] = TRUE;
		// $config['cas_debug'] = TRUE; // <--  use this to enable phpCAS debug mode

3. Tambahkan *cas* kedalam *autoload* seperti contoh berikut:

		$autoload['libraries'] = array('cas');

## Penggunaan
Sekarang, anda dapat mencoba dengan menambahkan di *controller* sebagai berikut:

		function showLogin() {
			if($this->session->has_userdata('name_session')) { //check session id
				redirect('/'); //redirect to index
			} else {
				$this->load->view('signing_process'); //redirect to login page
			}
		}

		function signing_process()
		{
			$this->cas->authenticate(); //check sso authentication
			$user = $this->cas->user(); //get data user

			$this->load->model('user'); //get user model
			$login = $this->user->login_sso($user->userlogin); //check user to database
			if($login) {
				redirect("/", "refresh"); //redirect to index
			} else {
				redirect("/logout", "refresh"); //redirect to logout
			}
		}

		function logout()
		{
			$this->load->model('user'); //get user model
			$this->user->logout(); //destroy auth
			redirect('/login'); //redirect to login page
		}

Untuk model, disesuaikan dengan model yang telah ada dalam aplikasi anda. sebagai contoh:

		<?php
		defined('BASEPATH') or exit('No direct script access allowed');

		class User extends CI_Model
		{
			private $_table = "users";
			const SESSION_KEY = 'name_session';

			public function login_sso($email)
			{
				$this->db->where('email', $email);
				$query = $this->db->get($this->_table);
				$user = $query->row();

				if (!$user) {
					return FALSE;
				}
				$this->session->set_userdata([self::SESSION_KEY => $user]);
				$this->_update_last_login($user->id);

				return $this->session->has_userdata(self::SESSION_KEY);
			}

			public function logout()
			{
				$this->session->unset_userdata(self::SESSION_KEY);
				return !$this->session->has_userdata(self::SESSION_KEY);
			}

			private function _update_last_login($id)
			{
				$data = [
					'updated_at' => date("Y-m-d H:i:s"),
				];

				return $this->db->update($this->_table, $data, ['id' => $id]);
			}
		}

## Thanks to

1. PhpCAS
2. eliasdorneles/code-igniter-cas-library
3. egiesem/CAS-SSO-Codeigniter-3-Library-