---
title: "Contato"
---

Entre em contato conosco.

---

<form id="contact-form">
  <label for="name">Nome:</label>
  <input type="text" id="name" name="name" required><br>

  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required><br>

  <label for="message">Mensagem:</label>
  <textarea id="message" name="message" required></textarea><br>

  <button type="submit">Enviar</button>
</form>

<script>
document.getElementById('contact-form').addEventListener('submit', function(e) {
  e.preventDefault();
  // Code to send to Lambda API
  alert('Form submitted!'); // Placeholder
});
</script>

---

## Siga-nos no Facebook

<div style="display:flex;justify-content:center;margin:1rem 0;">
  <iframe
    src="https://www.facebook.com/plugins/page.php?href=https%3A%2F%2Fwww.facebook.com%2Fcesfraternite&tabs=timeline&width=340&height=500&small_header=true&adapt_container_width=true&hide_cover=false&show_facepile=false"
    width="340" height="500"
    style="border:none;overflow:hidden;"
    scrolling="no" frameborder="0" allowfullscreen="true"
    allow="autoplay; clipboard-write; encrypted-media; picture-in-picture; web-share">
  </iframe>
</div>
