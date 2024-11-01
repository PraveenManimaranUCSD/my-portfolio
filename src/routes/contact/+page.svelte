<svelte:head>
    <title>Contact Me</title>
</svelte:head>



<h1>Contact</h1>
<form action="mailto:pravmanimaran@gmail.com" method="POST" enctype="text/plain">
    <label for="email">Your Email:</label>
    <input type="email" name="email" id="email" required />
    <br /><br />
    <label for="subject">Subject:</label>
    <input type="text" name="subject" id="subject" />
    <br /><br />
    <label for="body">Message:</label>
    <textarea name="body" id="body" rows="5"></textarea>
    <br /><br />
    <button type="submit">Submit</button>
</form>
