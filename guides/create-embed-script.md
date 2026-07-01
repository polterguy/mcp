---
name: create-embed-script
when_to_use: Producing the <script> embed tag that puts Magic's standard AI chatbot widget on a website (with options like theme, colors, header, references, attachments).
---

# Create an embed script for an AI chatbot

This is the ONLY way to create an embed script for the standard AI chatbot widget — there is no
tool for it, just this recipe. Build the `<script>` tag and hand it to the user so they can embed
the chatbot on their website.

> This produces the **standard embedded chatbot widget**. For a fully hand-built custom GUI, see
> the `create-custom-chatbot-gui` guide instead.

Gather the following (defaults shown):

1. `type` — full name of the machine-learning type (required).
2. `header` — chat window header. Default `'Ask me anything'`.
3. `button` — text of the button that opens the window. Default `'AI Chat'`.
4. `rtl` — right-to-left UI. Default `false`.
5. `color` — CSS color for the chatbot's text. Default `#fefefe`.
6. `start` — starting color of the background linear-gradient. Default `#0000ff`.
7. `end` — ending color of the background linear-gradient. Default `#0000d0`.
8. `link` — text color of hyperlinks inside the chat window.
9. `theme` — theme/css/skin name. Default `modern-bubbles`. To use another theme, verify it
   exists with `list-chatbot-themes`.
10. `references` — return references for training snippets with URL citations. Default `false`.
11. `placeholder` — textbox placeholder. Default `'Write your question here ...'`.
12. `position` — `right` or `left`. Default `right`.
13. `clear_button` — adds a button to clear the session and start a new one. Default `false`.
14. `copyButton` — adds a "copy response" button per LLM response. Default `false`.
15. `follow_up` — adds JS to support follow-up questions (the system instruction must be set up
    for this). Use `true` unless told otherwise.
16. `new_tab` — open all hyperlinks in a new tab. Default `false`.
17. `code` — syntax-highlight code segments returned by the LLM. Default `false`.
18. `animation` — animation CSS selector (currently only `ainiro_blink`). Default `null`.
19. `popup` — popup text shown 5 seconds after load. Default `"👋 Hello there, need help? 😃"`.
20. `hidden` — makes the chatbot button invisible so you can supply your own trigger button.
    Default `false`.
21. `sticky` — reopen the chatbot automatically when navigating to a new page if it was open on
    the previous page. Default `false`.
22. `attachments` — let users attach/upload files to the LLM. Default `false`.
23. `extra` — additional extra parameters for complex per-window integrations. Default `null`.
24. `history` — let users see historical requests and continue previous conversations.

The type must be an existing machine-learning type. List types with `list-types`; create one
with `create-type`.

## Output

Once the user is satisfied, return this HTML:

```html
<script src="[BACKEND_URL_HERE]/magic/system/openai/include-chatbot.js?rtl=[RTL]&clear_button=[CLEAR_BUTTON]&follow_up=[FOLLOW_UP]&copyButton=[COPY_BUTTON]&new_tab=[NEW_TAB]&code=[CODE]&references=[REFERENCES]&position=[POSITION]&type=[MACHINE_LEARNING_TYPE]&header=[HEADER]&popup=[POPUP]&button=[BUTTON_TEXT]&placeholder=[PLACEHOLDER]&color=[TEXT_COLOR]&start=[START_BG_VOLOR]&end=[END_BG_COLOR]&link=[LINK_COLOR]&theme=[THEME]&sticky=[STICKY]&attachments=[ATTACHMENTS]&history=[HISTORY]" defer></script>;
```

Replace `[BACKEND_URL_HERE]`, `[RTL]`, etc. according to the user's choices.

## Finishing — optional landing page

After creating the embed script, offer to create an HTML landing page with the embed script
injected. If the user agrees, ask for a URL to scrape (`invoke-http` GET, or a generated scrape
tool), use the scraped content to build a high-quality demo landing page, inject the embed
script, and save it with `create-file`. Use the machine-learning type name + `.html` as the
filename unless told otherwise. Return the URL to the landing page as Markdown.
