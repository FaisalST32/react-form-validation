# use-validation-react

A simple react hook for all your form validations.

### How to install:

Install via npm 

```
npm i use-validation-react
```

or via yarn

```
yarn add use-validation-react
```

### How to use:

Library exports useValidation hook. The hook accepts one argument, validation records

```
const validations = {
  name: [
    {
      rule: ValidationRule.Required,
      errorMessage: 'Please provide a valid first name',
    },
  ],
  about: [
    {
      rule: ValidationRule.MinLength,
      errorMessage: 'About should be 20 characters or more',
      meta: 20
    },
  ],
  phoneNumber: [
    {
      rule: ValidationRule.Phone,
      errorMessage: 'Please provide a valid phone number',
    },
  ],
  email: [
    {
      rule: ValidationRule.Email,
      errorMessage: 'Please provide a valid email',
    },
  ],
}
```

You could then use these validation rules inside your component as below
Note: The property names in your object that you need to validate should match the property names inside your validation rules.

```
const Profile = () => {
  const [profileData, setProfileData] = useState({
    name: '',
    about: '',
    phoneNumber: '',
    email: '',
  });
  
  const {
    validationResult,
    isValidated,
    isFormValid,
    validateFormElement,
    clearValidations,
    validateAll
  } = useValidation(updateProfileValidations);
  
  return (
    <>
      <input type="text" value={profileData.name} />
      ...
    </>
  )
}
```
To check if your form is valid, you simply call `isFormValid` with the object to validate

```
const formIsValid = await isFormValid(profileData);
```

This will run validation for your whole form and return true or false.

You may also want to show error messages for each input that is invalid.

You could access the validation result for each input using `validationResult`

e.g., To show an error message for name input, you could use the following:
Note: Validation are only run on demand. To know if validations have been run
use `isValidated` boolean.

```
<input type="text" value={profileData.name} />
{isValidated && !validationResult['name'].isValid && (<div>{validationResult['name'].messages[0]}</div>)}
```

This will display an error message if name input has been validated and the validation returned false.

### What can you use?
| Property | Type | Description |
|:--------------------|:-----------------------|:-----------------------|
| `validationResult` | `Record<string, { isValid: boolean; messages?: string[]; }>` | A key value pair that is populated once the validations have run. The value contains two properties: `isValid`: Boolean specifying whether an input is valid or not `messages`: An array of all the messages for an input for which the validations have failed |
| `isValidated` | `boolean` | A boolean specifying whether validations have run. Validations are considered to have run if, `isFormValid`, `validateFormElement` or `validateAll` have been called |
| `isFormValid` | `(form: any) => Promise<boolean>` | Method that accepts the object to be validated as argument and returns a Promise that resolves to a true if all validations pass and false if one or more validations fail. |
| `validateFormElement` | `(form: any, elementKey: string) => Promise<void>` | Sometimes you may want to only run validations for a particular form element. This method accepts two arguments, object to validate and the specific property name. This will update the `validationResult` with the result of this particular input. |
| `clearValidations` | `() => void` | Calling this method clears the `validationResult`. |
| `validateAll` |  `(form: any) => Promise<Record<string, { isValid: boolean; messages?: string[]; }>>` | Does the same thing as `isFormValid`, but returns the `validationResult` instead of a boolean. |
| `isElementValid` |  `(elementKey: string) => boolean` | Checks if the form element with the given key is valid or not. Note: Calling this method does not run the validation rules for that particular form element. To run validations, use `validateFormElement` |

### Available Validation Rules

| Rule | Description | Notes |
|:-----|:------------|:------|
|Required|Returns true only if the input has non-empty value||
|Email|Returns true only if the value matches email regex|Regex used: `/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/`|
|Number|Returns true only if the value is a number or can be parsed to a number||
|Phone|Returns true if the value is a formatted phone number|May not work for all kinds of phone numbers. For reference, the regex used to validate is `/^(\+[0-9]+)?\(?([0-9]{3})\)?([ .-]?)([0-9]{3})\3([0-9]{4})$/`|
|NoSpaces|Returns true if the value contains no spaces||
|Regex|Returns true if the value matches the passed regex|In addition to `rule` and `errorMessage` properties, you need to pass the regex using the `meta` property.|
|MinimumLength| Returns true if the value length is >= the one passed| In addition to `rule` and `errorMessage` properties, you need to pass the length using the `meta` property.|
|MaximumLength| Returns true if the value length is <= the one passed| In addition to `rule` and `errorMessage` properties, you need to pass the length using the `meta` property.|
|Remote|Sometimes you want to write your own validation logic. Use this validation rule for that. You need to pass a function of type `(value: string \| number) => Promise<boolean>` to the meta property of your rule.| This rule can be useful when you need to validate a value on the server e.g., check if email exists, etc.|



