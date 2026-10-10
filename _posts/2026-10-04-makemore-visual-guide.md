---
layout: post
title: "Makemore: An Interactive Visual Guide"
date: 2026-10-04
tags: [ai, neural-networks, makemore]
---

An interactive visual guide to Andrej Karpathy’s makemore series, covering
layer shapes, initialization, BatchNorm, and manual backpropagation in Parts 1–4.

Use the guide directly below. Its HTML is maintained in my
[PythonCode repository](https://github.com/abg011/PythonCode/blob/master/scripts/AKarp/makemore/makemore-at-a-glance.html).

<p id="makemore-status" role="status">Loading the interactive guide…</p>
<noscript><p>Enable JavaScript to view the interactive guide.</p></noscript>
<iframe
  id="makemore-guide"
  title="Makemore interactive visual guide"
  sandbox="allow-scripts allow-popups allow-popups-to-escape-sandbox"
  style="display:none;width:100%;height:85vh;min-height:600px;border:1px solid #dbe2db;border-radius:10px;background:#f6f5f0;"
></iframe>
<script>
(function () {
  var status = document.getElementById('makemore-status');
  var frame = document.getElementById('makemore-guide');
  var source = 'https://raw.githubusercontent.com/abg011/PythonCode/7fb0ef6cb461f97b27de9fb960497fabdd1d3171/scripts/AKarp/makemore/makemore-at-a-glance.html';
  fetch(source)
    .then(function (response) {
      if (!response.ok) throw new Error('Unable to load guide');
      return response.text();
    })
    .then(function (html) {
      frame.onload = function () { status.hidden = true; };
      frame.srcdoc = html;
      frame.style.display = 'block';
    })
    .catch(function () {
      status.textContent = 'The guide could not load. Please refresh to try again, or use the repository link above.';
    });
})();
</script>
