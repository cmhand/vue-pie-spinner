# vue-pie-spinner
An SVG pie spinner for games

## Requirements

1. Use Vueify

## Usage

### app.js
```
import VuePieSpinner from 'vue-pie-spinner';

Vue.component('vue-pie-spinner', VuePieSpinner);
```

### vue file
```
<vue-pie-spinner ref="spinner" @onSpin="spun()"></vue-pie-spinner>

methods: {
	spinIt() {
		this.$refs.spinner.spin();
	},
	spun() {
		// spun!
	}
}
```

You can access the spinner using a child reference, and call the spin method.

When the spin method animation is complete, the `onSpin` event will be emitted.