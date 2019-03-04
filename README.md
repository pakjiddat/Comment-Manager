<h3>Introduction</h3>
<p>The Comment Manager is a Php script that allows parsing method <a href='https://en.wikipedia.org/wiki/PHPDoc'>Doc Block comments</a>. It also allows validating parameter values and return values using information in the Doc Block comments.</p>

<h3>Features</h3>

<ol>

<li><h5><b>Method parameter validation</b></h5>
<p>If you have added the following comment to a menthod:</p>

<pre>
<b>@param string $my_param [val1,val2,val3]</b>
</pre>

<p>Then the Comment Manager script can be used to check if the value given for the parameter, $my_param is in the list val1,val2,val3.</p>

<p>The script can validate range of numbers, parameter types, lists, emails, json and arrays. Method return values can also be validated based on information in DocBlock comments</p>
</li>

<li><h5><b>Extracting method description</b></h5>
<p>The Comment Manager script can be used to extract the description text given in method Doc Bloc comments. Long and short method description can be extracted</p>
</li>

<li><h5><b>Validating method context</b></h5>
<p>The Comment Manager script can be used to check if a class method is being called from the command line, from browser, as an API function or test function etc, based on information in the <a href='http://docs.phpdoc.org/references/phpdoc/tags/internal.html'>@internal tag</a></p>
</li>

<li><h5><b>Custom Validation</b></h5>
<p>The Comment Manager script allows validating parameter values using custom validation functions</p>
</li>

<li><h5><b>Supported tags</b></h5>
<p>Currently the Comment Manager script can be used to extract the following tags from DocBloc comments: <a href='http://docs.phpdoc.org/references/phpdoc/tags/param.html'>@param</a>, <a href='http://docs.phpdoc.org/references/phpdoc/tags/version.html'>@version></a>, <a href='http://docs.phpdoc.org/references/phpdoc/tags/internal.html'>@internal</a>, <a href='http://docs.phpdoc.org/references/phpdoc/tags/return.html'>@return</a> and the <a href='http://docs.phpdoc.org/guides/docblocks.html#description'>long and short method description</a>.</p>

<p>The Comment Manager script can be easily extended to extract other tags from method comments</p>
</li>

<li><h5><b>Use in MVC frameworks</b></h5>
<p>The Comment Manager package can be used in MVC (Model View Controller) frameworks for performing automatic validation of method parameters based on information in the method Doc Block comments</p>
</li>

<li><h5><b>PSR Standard</b></h5>
<p>The Comment Manager package code is compliant with the <a href='https://www.php-fig.org/psr/psr-2/'>PSR-2 coding style</a></p>
</li>

</ol>

<h3>Example Use</h3>

<h5><b>Example 1</b></h5>
<p>The following example shows how to call a function safely with parameter and return value validation:</p>

<pre>
<b>
/** The safe_function_caller closure is fetched from the CommentManager class */
$safe_function_caller       = CommentManager::GetClosure();
/** The parameters for the test function */
$parameters                 = array(
                                  "number1" => 30,
                                  "number2" => 10,
                                  "number3" => 10,
                                   "data" => array(
                                                 "type" => "integer",
                                                  "random_string" => "The result of adding the three integers is: ";
                                            )
                              );
                              
/** The function that provides custom validation for the test function parameters */
$validation_callback        = array($this, "CustomValidation");
                                      
/** The callback */
$callback                   = array(
                                  "class_name" => get_class(),
                                  "class_object" => $this,
                                  "function_name" => "AddNumbers", 
                                  "parameters" => $parameters, 
                                  "context" => "cli"
                              );
try {                                          
   /** The test function is called through the safe function caller */
   $result                  = $safe_function_caller($callback, $validation_callback);            
}
catch (\Error $e) {
    /** The error message is displayed and the script ends */
    die($e->getMessage());
}
     
/** The result of adding the numbers is displayed */
echo $result['random_string'] . $result['sum'];
</b>
</pre>

<p class="mt-4">In the above exampe, <b>CustomValidation</b> is a user defined function that performs custom validation. <b>AddNumbers</b> is the function to validate. It takes 3 numbers and a random string as parameters. It returns the random string as it is and also the sum of the three numbers. The custom validation function validates the return value by checking if the length of the random string is within range. The type and value of the parameters is checked by the Comment Manager classes. See the example file <b>CommentManagerTest.php</b> for the complete example.</p>

<h5><b>Example 2</b></h5>
<p>The following example shows how to extract method DocBlock comments:</p>

<pre>
<b>
/** The Parser class is included */
require("Parser.php");
/** An object of the parser class is created */
$parser = new Parser();
/** The method doc block comments are parsed */
$parsed_comments          = $parser->ParseMethodDocBlockComments($class_name, $function_name);
print_R($parsed_comments);
</b>
</pre>

<p>In the above example, $class name and $function_name is the method to check. The following output is produced:</p>

<pre>
<b>
Array
(
    [description] => Array
        (
            [short] => This function adds the three numbers given as parameters.
                       It returns the sum of the numbers and a random string.
                       The random string is given as the last parameter
            [long] => 
        )

    [version] => Array
        (
            [since] => 
            [version] => 
        )

    [internal] => Array
        (
            [context] => cli
        )

    [parameters] => Array
        (
            [0] => Array
                (
                    [type] => int
                    [variable_name] => number1
                    [rule] => range
                    [description] => the first number
                    [rule_data] => 1-100
                )

            [1] => Array
                (
                    [type] => int
                    [variable_name] => number2
                    [rule] => range
                    [description] => the second number
                    [rule_data] => 1-100
                )

            [2] => Array
                (
                    [type] => int
                    [variable_name] => number3
                    [rule] => range
                    [description] => the third number
                    [rule_data] => 1-100
                )

            [3] => Array
                (
                    [type] => array
                    [variable_name] => data
                    [rule] => 
                    [description] => contains the type of the numbers and a string
                    [rule_data] => 
                    [values] => Array
                        (
                            [0] => Array
                                (
                                    [variable_name] => type
                                    [type] => string
                                    [rule] => list
                                    [rule_data] => integer,float
                                    [description] => the type of number to be added
                                )

                            [1] => Array
                                (
                                    [variable_name] => random_string
                                    [type] => string
                                    [rule] => custom
                                    [rule_data] => 
                                    [description] => a random string that is returned by the function
                                )

                        )

                )

        )

    [return_value] => Array
        (
            [type] => array
            [variable_name] => result
            [rule] => 
            [description] => the result of the function
            [rule_data] => 
            [values] => Array
                (
                    [0] => Array
                        (
                            [variable_name] => sum
                            [type] => int
                            [rule] => range
                            [rule_data] => 1-50
                            [description] => the sum of the three numbers
                        )

                    [1] => Array
                        (
                            [variable_name] => random_string
                            [type] => string
                            [rule] => 
                            [rule_data] => 
                            [description] => the random string
                        )

                )

        )

    [function_name] => AddNumbers
)
</b>
</pre>

<h3>List of files</h3>
<p>The Comment Manager package consists of the following files:</p>
<ul>
  <li><b>CommentManager.php</b>. It provides a closure function for safe calling of class methods. It also provides functions for parsing and validating method parameters and return value</li>
  <li><b>Parser.php</b>. It provides a single function for parsing all method DocBlock comments</li>
  <li><b>DescriptionParser.php</b>. It provides functions for extracting long and short method description as well as the internal and version tags</li>
  <li><b>ParameterParser.php</b>. It provides functions for parsing the <b>@param</b> tag</li>
  <li><b>Validator.php</b>. It provides functions for validating method parameters and method return value using information in DocBloc comments</li>
  <li><b>VariableValidator.php</b>. It provides functions for validating different types of variables. For example integer, string, boolean and arrays</li>
</ul>

<h3>Installation</h3>

<ol>

<li>
<h5>Install using composer:</h5>
<p>Run the command: <b>composer require nadirlc/comment-manager</b></p>
<p><b>OR</b></p>
</li>

<li>
<h5>Download from the <a href='https://github.com/nadirlc/comment-manager'>Comment Manager GitHub Repository</a></h5>
<p>Run the command: <b>git clone https://github.com/nadirlc/comment-manager.git</b></p>
</li>
</ol>
