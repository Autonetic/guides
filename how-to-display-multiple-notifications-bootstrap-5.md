Bootstrap 5 Multiple Notifications

In Bootstrap 5, you can display multiple notifications using the `.alert` class and its variants. Here are some key points to consider:

1. **Initialization**: You can initialize elements as alerts using the JavaScript API or by adding the `data-bs-alert` attribute to the element.
2. **Dismissal**: To dismiss an alert, add the `data-bs-dismiss="alert"` attribute to a close button or any other element that triggers the dismissal. You can also use the JavaScript API to dismiss alerts programmatically.
3. **Multiple alerts**: To display multiple notifications, simply add multiple `.alert` elements to your HTML. You can use different variants (e.g., `.alert-success`, `.alert-warning`, `.alert-danger`) to customize the appearance and behavior of each alert.
4. **CSS variables**: Bootstrap 5 uses local CSS variables for alerts, which allows for real-time customization. You can override these variables using Sass or CSS.
5. **JavaScript events**: The alert plugin exposes several events for hooking into alert functionality, such as `closed.bs.alert` and `show.bs.alert`.

**Example Code**

Here's an example of displaying multiple notifications with different variants:
```html
<div class="alert alert-success" role="alert">
  Success alert!
</div>
<div class="alert alert-warning" role="alert">
  Warning alert!
</div>
<div class="alert alert-danger" role="alert">
  Danger alert!
</div>
```
You can also use the JavaScript API to initialize and dismiss alerts programmatically:
```javascript
const alerts = document.querySelectorAll('.alert');

alerts.forEach((alert) => {
  alert.addEventListener('click', (event) => {
    if (event.target.classList.contains('btn-close')) {
      alert.classList.remove('show');
      alert.classList.add('fade');
      setTimeout(() => {
        alert.remove();
      }, 500);
    }
  });
});
```
**Tips and Variations**

* Use the `.alert-dismissible` class to add extra padding to the right of the alert and position the close button.
* Customize the appearance of alerts using CSS variables or Sass.
* Use flexbox utilities and Bootstrap Icons to create alerts with icons.
* Consider using a local SVG sprite to easily reference the same icons repeatedly.
* Animate alerts when dismissing them by adding the `.fade` and `.show` classes.

By following these guidelines and examples, you can effectively display multiple notifications using Bootstrap 5's alert system.