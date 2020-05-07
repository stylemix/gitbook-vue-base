# Form fields

### Create a form component

Create a form component with the following structure:

* add `FormMixin`
* declare fields with `this.setFields()` method
* declare `this.model` object with initial values
* add `<fields />` component where fields should be rendered

{% code title="ContactForm.vue" %}
```markup
<template>
  <form method="post" @submit.prevent="submit">
    <fields />
    <button
      type="submit"
      class="btn btn-primary">
      Send
    </button>
  </form>
</template>

<script>
import { FormMixin } from 'stylemix-base'

export default {
  mixins: [ FormMixin ],

  data() {
    return {
      model: {
        // ...initial model values
      },
    }
  },

  mounted() {
    this.setFields([
      // ..field definitions
    ])
  },
  
  methods: {
    submit() {
    }
  }
}
</script>
```
{% endcode %}

### Field definition

```javascript
{
  // property name in model
  attribute: "your_name",
  
  // human visible label
  label: "Your name"
  
  // component identifier (should be registered)
  component: "text-field",
  
  // other field specific options
  required: true,
  minLength: 3,
}
```

### Submitting form

You can use `this.model` values to send form data

{% code title="ContactForm.vue" %}
```markup
<script>
export default {
  // ...rest code
  methods: {
    submit() {
      this.errors.clear()

      // send form data
      this.$http.post('/contact-form', { ...this.model })
        .then(() => {
          alert('Sucessfully submitted!')
        })
    }
  }
}
</script>
```
{% endcode %}

#### Values as FormData

Sometimes it is required to send form data as `multipart/form-data`, i.e. when contains file attachments.

{% code title="ContactForm.vue" %}
```markup
<script>
export default {
  // ...rest code
  methods: {
    submit() {
      this.errors.clear()
      
      let formData = this.formData(this.model)
      // since PUT request does not support multipart/form-data
      // some frameworks like Laravel support method hinting
      formData.append('_method', 'PUT')

      // send form data
      this.$http.post('/contact-form', formData)
        .then(() => {
          alert('Sucessfully submitted!')
        })
    }
  }
}
</script>
```
{% endcode %}

### Field errors

To set field errors use `this.setValidationErrors()` method:

```javascript
this.setValidationErrors({
  your_name: ['Please, enter you name.'],
  your_email: ['Entered value is not valid email.']
})
```

