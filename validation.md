# Validation

- [Basic Usage](#basic-usage)
- [Working With Error Messages](#working-with-error-messages)
- [Error Messages & Views](#error-messages-and-views)
- [Available Validation Rules](#available-validation-rules)
- [Custom Error Messages](#custom-error-messages)
- [Custom Validation Rules](#custom-validation-rules)

<a name="basic-usage"></a>
## Basic Usage

Laravel ships with a simple, convenient facility for validating data and retrieving validation error messages via the `Validation` class.

**Basic Validation Example**

	$validator = Validator::make(
		['name' => 'Dayle'],
		['name' => 'required|min:5']
	);

The first argument passed to the `make` method is the data under validation. The second argument are the validation rules that should be applied to the data.

Multiple rules may be delimited using either a "pipe" character, or as separate elements of an array.

**Using Arrays To Specify Rules**

	$validator = Validator::make(
		['name' => 'Dayle'],
		['name' => ['required', 'min:5']]
	);

Once the a `Validator` instance has been created, the `fails` (or `passes`) method may be used to perform the validation.

	if ($validation->fails())
	{
		// The given data did not pass validation
	}

If validation has failed, you may retrieve the error messages from the validator.

	$messages = $validator->messages();

**Validating Files**

The `Validator` class provides several rules for validating files, such as `size`, `mimes`, and others. When validating files, you may simply pass them into the validator with your other data.

<a name="working-with-error-messages"></a>
## Working With Error Messages

After calling the `messages` method on a `Validator` instance, you will receive a `MessageBag` instance, which has a variety of convenient methods for working with error messages.

**Retrieving The First Error Message For A Field**

	echo $messages->first('email');

**Retrieving All Error Messages For A Field**

	foreach ($messages->get('email') as $message)
	{
		//
	}

**Retrieving All Error Messages For All Fields**

	foreach ($messages->all() as $message)
	{
		//
	}

**Determining If Messages Exist For A Field**

	if ($messages->has('email'))
	{
		//
	}

**Retrieving An Error Message With A Format**

	echo $messages->first('email', '<p>:message</p>');

> **Note:** By default, messages are formatted using Bootstrap compatible syntax.

**Retrieving All Error Messages With A Format**

	foreach ($messages->all('<li>:message</li>') as $message)
	{
		//
	}

<a name="error-messages-and-views"></a>
## Error Messages & Views

Once you have performed validation, you will need an easy way to get the error messages back to your views. This is conveniently handled by Laravel. Consider the following routes as an example:

	Route::get('register', function()
	{
		return View::make('user.register');
	});

	Route::post('register', function()
	{
		$rules = [...];

		$validator = Validator::make(Input::all(), $rules);

		if ($validator->fails())
		{
			return Redirect::to('register')->withErrors($validator);
		}
	});

Note that when validation fails, we pass the `Validator` instance to the Redirect using the `withErrors` method. This method will flash the error messages to the session so that they are available on the next request.

However, notice that we do not have to explicitly bind the error messages to the view in our GET route. This is because Laravel will always check for errors in the session data, and automatically bind them to the view if they are available. **So, it is important to note that an `$errors` variable will always be available in all of your views, on every request**, allowing you to conveniently assume the `$errors` variable is always defined and can be safely used. The `$errors` variable will be an instance of `MessageBag`.

So, after redirection, you may utilize the automatically bound `$errors` variable in your view:

	<?php echo $errors->first('email'); ?>

<a name="available-validation-rules"></a>
## Available Validation Rules

Below is a list of all available validation rules and their function:

- [Accepted](#rule-accepted)
- [Active URL](#rule-active-url)
- [After (Date)](#rule-after)
- [Alpha](#rule-alpha)
- [Alpha Dash](#rule-alpha-dash)
- [Alpha Numeric](#rule-alpha-num)
- [Before (Date)](#rule-before)
- [Between](#rule-between)
- [Confirmed](#rule-confirmed)
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
- [Numeric](#rule-numeric)
- [Regular Expression](#rule-regex)
- [Required](#rule-required)
- [Required With](#rule-required-with)
- [Same](#rule-same)
- [Size](#rule-size)
- [Unique (Database)](#rule-unique)
- [URL](#rule-url)

<a name="rule-accepted"></a>
#### accepted

The field under validation must be _yes_, _on_, or _1_. This is useful for validation "Terms of Service" acceptance.

<a name="rule-active-url"></a>
#### active_url

The field under validation must be a valid URL according to the `checkdnsr` PHP function.

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

The field under validation must be a value preceeding the given date. The dates will be passed into the PHP `strtotime` function.

<a name="rule-between"></a>
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

<a name="rule-confirmed"></a>
#### confirmed

The field under validation must have a matching field of `foo_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input.

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

<a name="rule-image"></a>
#### image

The file under validation must be an image (jpg, png, bmp, or gif)

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

	'photo' => 'mimes:jpg,bmp,png'

<a name="rule-min"></a>
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

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

	$messages = [
		'required' => 'The :attribute field is required.',
	];

	$validator = Validator::make($input, $rules, $messages);

*Note:* The `:attribute` place-holder will be replaced by the actual name of the field under validation. You may also utilize other place-holders in validation messages.

**Other Validation Place-Holders**

	$messages = [
		'same'    => 'The :attribute and :other must match.',
		'size'    => 'The :attribute must be exactly :size.',
		'between' => 'The :attribute must be between :min - :max.',
		'in'      => 'The :attribute must be one of the following types: :values',
	];

Sometimes you may wish to specify a custom error messages only for a specific field:

**Specifying A Custom Message For A Given Attribute**

	$messages = [
		'email.required' => 'We need to know your e-mail address!',
	];

In some cases, you may wish to specify your custom messages in a language file instead of passing them directly to the `Validator`. To do so, add your messages to `custom` array in the `app/lang/xx/validation.php` language file.

**Specifying Custom Messages In Language Files**

	'custom' => [
		'email' => [
			'required' => 'We need to know your e-mail address!',
		],
	],

<a name="custom-validation-rules"></a>
## Custom Validation Rules

Laravel provides a variety of helpful validaton rules; however, you may wish to specify some of your own. One method of registering custom validation rules is using the `Validator::extend` method:

**Registering A Custom Validation Rule**

	Validator::extend('foo', function($attribute, $value, $parameters)
	{
		return $value == 'foo'
	});

The custom validator Closure receives three arguments: the name of the `$attribute` being validated, the `$value` of the attribute, and an array of `$parameters` passed to the rule.

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

	Validator::resolver(function()
	{
		return new CustomValidator;
	});

When creating a custom validation rules, you may sometimes need to define custom place-holder replacements for error messages. You may do so by creating a custom Validator as described above, and adding a `replaceXXX` function to the validator.

	protected function replaceFoo($message, $attribute, $rule, $parameters)
	{
		return str_replace(':foo', $parameters[0], $message);
	}