# Validation

- [基本用法](#basic-usage)
- [处理错误消息](#working-with-error-messages)
- [错误消息 & 视图](#error-messages-and-views)
- [可用的验证规则集](#available-validation-rules)
- [自定义错误消息](#custom-error-messages)
- [自定义验证规则](#custom-validation-rules)

<a name="basic-usage"></a>
## 基本用法

Laravel 通过 `Validation` 类附带了一个简单, 方便的用于验证数据和检索验证错误消息的实现.

**基本的验证例子**

	$validator = Validator::make(
		array('name' => 'Dayle'),
		array('name' => 'required|min:5')
	);

传给 `make` 方法的第一个参数是需要进行验证的数据. 第二个参数则是将被应用于数据上的验证规则.

多重验证规则可以通过 "管道" 符进行区分, 或者分离为一个数组来实现.

**使用数组来指定规则**

	$validator = Validator::make(
		array('name' => 'Dayle'),
		array('name' => array('required', 'min:5'))
	);

一旦一个 `验证器` 实例被创建, 就可以使用 `fails` (或 `passes`) 方法来执行该验证.

	if ($validator->fails())
	{
		// The given data did not pass validation
	}

如果验证失败, 你可以从验证器实例中取得错误消息.

	$messages = $validator->messages();

你也可以访问一个包含已失败的验证规则的数组, 不包含错误消息. 要这样做的话, 请使用 `failed` 方法:

	$failed = $validator->failed();

**验证文件**

`Validator` 类提供了许多规则来验证文件, 例如 `size`, `mimes` 等等. 在验证文件的时候, 只需简单的与其他数据一起传入验证器即可.

<a name="working-with-error-messages"></a>
## 处理错误消息

在一个 `Validator` 实例上调用了 `messages` 方法之后, 你将得到一个 `MessageBag` 实例, 该实例拥有各种各样方便的方法来处理错误消息.

**检索一个字段的第一个错误消息**

	echo $messages->first('email');

**检索一个字段的所有错误消息**

	foreach ($messages->get('email') as $message)
	{
		//
	}

**检索所有字段的所有错误消息**

	foreach ($messages->all() as $message)
	{
		//
	}

**判断是否包含与指定字段有关的错误消息**

	if ($messages->has('email'))
	{
		//
	}

**按照指定格式来检索错误消息**

	echo $messages->first('email', '<p>:message</p>');

> **注意:** 默认情况下, 错误消息均按 Bootstrap 兼容语法进行格式化.

**按照指定格式来检索所有错误消息**

	foreach ($messages->all('<li>:message</li>') as $message)
	{
		//
	}

<a name="error-messages-and-views"></a>
## 错误消息 & 视图

一旦你完成了一次验证, 你需要一个简便的方法来将错误消息往视图里面回填. Laravel 能很方便的对此进行处理. 请看如下路由的例子:

	Route::get('register', function()
	{
		return View::make('user.register');
	});

	Route::post('register', function()
	{
		$rules = array(...);

		$validator = Validator::make(Input::all(), $rules);

		if ($validator->fails())
		{
			return Redirect::to('register')->withErrors($validator);
		}
	});

注意一旦验证失败我们就会把 `Validator` 的实例通过 `withErrors` 方法传递给 Redirect. 该方法将会缓存错误消息到session里面去以便在下一次请求的时候能够被使用.

然而, 要注意的一点是我们并不一定需要在 GET 路由中明确地给视图绑定错误消息. 这是因为 Laravel 总会在session数据中查找错误消息, 如果可用的话则自动将它们绑定到视图中去. **所以, 一定要注意 `$errors` 变量在你的所有视图中都可使用, 在每次请求中**, 这允许你假定 `$errors` 变量已经被定义并且能被安全地拿来使用. `$errors` 变量将会是 `MessageBag` 的一个实例.

所以, 在重定向之后, 你就可以在你的视图中使用已经自动绑定好的 `$errors` 变量了:

	<?php echo $errors->first('email'); ?>

<a name="available-validation-rules"></a>
## 可用的验证规则集

如下是一个所有可用的验证规则的清单以及它们的功能:

- [Accepted](#rule-accepted)
- [Active URL](#rule-active-url)
- [After (Date)](#rule-after)
- [Alpha](#rule-alpha)
- [Alpha Dash](#rule-alpha-dash)
- [Alpha Numeric](#rule-alpha-num)
- [Before (Date)](#rule-before)
- [Between](#rule-between)
- [Confirmed](#rule-confirmed)
- [Date](#rule-date)
- [Date Format](#rule-date-format)
- [Different](#rule-different)
- [E-Mail](#rule-email)
- [Exists (Database)](#rule-exists)
- [Image (File)](#rule-image)
- [In](#rule-in)
- [Integer](#rule-integer)
- [IP Address](#rule-ip)
- [Max](#rule-max)
- [MIME Types](#rule-mimes)
- [Min](#rule-min)
- [Not In](#rule-not-in)
- [Numeric](#rule-numeric)
- [Regular Expression](#rule-regex)
- [Required](#rule-required)
- [Required If](#rule-required-if)
- [Required With](#rule-required-with)
- [Same](#rule-same)
- [Size](#rule-size)
- [Unique (Database)](#rule-unique)
- [URL](#rule-url)

<a name="rule-accepted"></a>
#### accepted

The field under validation must be _yes_, _on_, or _1_. This is useful for validating "Terms of Service" acceptance.

<a name="rule-active-url"></a>
#### active_url

The field under validation must be a valid URL according to the `checkdnsrr` PHP function.

<a name="rule-after"></a>
#### after:_date_

The field under validation must be a value after a given date. The dates will be passed into the PHP `strtotime` function.

<a name="rule-alpha"></a>
#### alpha

The field under validation must be entirely alphabetic characters.

<a name="rule-alpha-dash"></a>
#### alpha_dash

The field under validation may have alpha-numeric characters, as well as dashes and underscores.

<a name="rule-alpha-num"></a>
#### alpha_num

The field under validation must be entirely alpha-numeric characters.

<a name="rule-before"></a>
#### before:_date_

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function.

<a name="rule-between"></a>
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

<a name="rule-confirmed"></a>
#### confirmed

The field under validation must have a matching field of `foo_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input.

<a name="rule-date"></a>
#### date

The field under validation must be a valid date according to the `strtotime` PHP function.

<a name="rule-date-format"></a>
#### date_format:_format_

The field under validation must match the _format_ defined according to the `date_parse_from_format` PHP function.

<a name="rule-different"></a>
#### different:_field_

The given _field_ must be different than the field under validation.

<a name="rule-email"></a>
#### email

The field under validation must be formatted as an e-mail address.

<a name="rule-exists"></a>
#### exists:_table_,_column_

The field under validation must exists on a given database table.

**Basic Usage Of Exists Rule**

	'state' => 'exists:states'

**Specifying A Custom Column Name**

	'state' => 'exists:states,abbreviation'

You may also specify more conditions that will be added as "where" clauses to the query:

	'email' => 'exists:staff,email,account_id,1'

<a name="rule-image"></a>
#### image

The file under validation must be an image (jpeg, png, bmp, or gif)

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

The field under validation must be included in the given list of values.

<a name="rule-integer"></a>
#### integer

The field under validation must have an integer value.

<a name="rule-ip"></a>
#### ip

The field under validation must be formatted as an IP address.

<a name="rule-max"></a>
#### max:_value_

The field under validation must be less than a maximum _value_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

The file under validation must have a MIME type corresponding to one of the listed extensions.

**Basic Usage Of MIME Rule**

	'photo' => 'mimes:jpeg,bmp,png'

<a name="rule-min"></a>
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

The field under validation must not be included in the given list of values.

<a name="rule-numeric"></a>
#### numeric

The field under validation must have a numeric value.

<a name="rule-regex"></a>
#### regex:_pattern_

The field under validation must match the given regular expression.

**Note:** When using the `regex` pattern, it may be necessary to specify rules in an array instead of using pipe delimiters, especially if the regular expression contains a pipe character.

<a name="rule-required"></a>
#### required

The field under validation must be present in the input data.

<a name="rule-required-if"></a>
#### required_if:_field_,_value_

The field under validation must be present if the _field_ field is equal to _value_.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

The field under validation must be present _only if_ the other specified fields are present.

<a name="rule-same"></a>
#### same:_field_

The given _field_ must match the field under validation.

<a name="rule-size"></a>
#### size:_value_

The field under validation must have a size matching the given _value_. For string data, _value_ corresponds to the number of characters. For numeric data, _value_ corresponds to a given integer value. For files, _size_ corresponds to the file size in kilobytes.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

The field under validation must be unique on a given database table. If the `column` option is not specified, the field name will be used.

**Basic Usage Of Unique Rule**

	'email' => 'unique:users'

**Specifying A Custom Column Name**

	'email' => 'unique:users,email_address'

**Forcing A Unique Rule To Ignore A Given ID**

	'email' => 'unique:users,email_address,10'

<a name="rule-url"></a>
#### url

The field under validation must be formatted as an URL.

<a name="custom-error-messages"></a>
## Custom Error Messages

If needed, you may use custom error messages for validation instead of the defaults. There are several ways to specify custom messages.

**Passing Custom Messages Into Validator**

	$messages = array(
		'required' => 'The :attribute field is required.',
	);

	$validator = Validator::make($input, $rules, $messages);

*Note:* The `:attribute` place-holder will be replaced by the actual name of the field under validation. You may also utilize other place-holders in validation messages.

**Other Validation Place-Holders**

	$messages = array(
		'same'    => 'The :attribute and :other must match.',
		'size'    => 'The :attribute must be exactly :size.',
		'between' => 'The :attribute must be between :min - :max.',
		'in'      => 'The :attribute must be one of the following types: :values',
	);

Sometimes you may wish to specify a custom error messages only for a specific field:

**Specifying A Custom Message For A Given Attribute**

	$messages = array(
		'email.required' => 'We need to know your e-mail address!',
	);

In some cases, you may wish to specify your custom messages in a language file instead of passing them directly to the `Validator`. To do so, add your messages to `custom` array in the `app/lang/xx/validation.php` language file.

**Specifying Custom Messages In Language Files**

	'custom' => array(
		'email' => array(
			'required' => 'We need to know your e-mail address!',
		),
	),

<a name="custom-validation-rules"></a>
## Custom Validation Rules

Laravel provides a variety of helpful validation rules; however, you may wish to specify some of your own. One method of registering custom validation rules is using the `Validator::extend` method:

**Registering A Custom Validation Rule**

	Validator::extend('foo', function($attribute, $value, $parameters)
	{
		return $value == 'foo';
	});

> **Note:** The name of the rule passed to the `extend` method must be "snake cased".

The custom validator Closure receives three arguments: the name of the `$attribute` being validated, the `$value` of the attribute, and an array of `$parameters` passed to the rule.

You may also pass a class and method to the `extend` method instead of a Closure:

	Validator::extend('foo', 'FooValidator@validate');

Note that you will also need to define an error message for your custom rules. You can do so either using an inline custom message array or by adding an entry in the validation language file.

Instead of using Closure callbacks to extend the Validator, you may also extend the Validator class itself. To do so, write a Validator class that extends `Illuminate\Validation\Validator`. You may add validation methods to the class by prefixing them with `validate`:

**Extending The Validator Class**

	<?php

	class CustomValidator extends Illuminate\Validation\Validator {

		public function validateFoo($attribute, $value, $parameters)
		{
			return $value == 'foo';
		}

	}

Next, you need to register your custom Validator extension:

**Registering A Custom Validator Resolver**

	Validator::resolver(function($translator, $data, $rules, $messages)
	{
		return new CustomValidator($translator, $data, $rules, $messages);
	});

When creating a custom validation rule, you may sometimes need to define custom place-holder replacements for error messages. You may do so by creating a custom Validator as described above, and adding a `replaceXXX` function to the validator.

	protected function replaceFoo($message, $attribute, $rule, $parameters)
	{
		return str_replace(':foo', $parameters[0], $message);
	}
