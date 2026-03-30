<!DOCTYPE html>
      const walker = doc.createTreeWalker(root, NodeFilter.SHOW_TEXT, null);
      const textNodes = [];

      while (walker.nextNode()) {
        textNodes.push(walker.currentNode);
      }

      for (const node of textNodes) {
        if (shouldSkipNode(node)) continue;
        node.nodeValue = cleanTextValue(node.nodeValue, options);
      }

      if (options.removeEmptyInline) {
        removeEmptyInlineNodes(root);
      }

      return root.innerHTML
        .replace(/>\s+</g, "><")
        .replace(/\n{3,}/g, "\n\n")
        .trim();
    }

    function highlightedHtml(str) {
      const escaped = escapeHtml(str || "");
      return escaped.replace(/&amp;nbsp;/g, '<mark>&amp;nbsp;</mark>');
    }

    function updateStats(before, after) {
      const beforeCount = countNbsp(before);
      const afterCount = countNbsp(after);
      nbspBefore.textContent = beforeCount;
      nbspAfter.textContent = afterCount;
      nbspRemoved.textContent = Math.max(beforeCount - afterCount, 0);
      detectedBadge.textContent = `Detected unnecessary NBSP: ${beforeCount}`;
    }

    function runProcess() {
      const text = plainTextInput.value;
      const messy = plainTextToMessyHtml(text);
      const cleaned = cleanHtml(messy, {
        replaceNbsp: replaceNbsp.checked,
        collapseRuns: collapseRuns.checked,
        trimText: trimText.checked,
        removeEmptyInline: removeEmptyInline.checked,
      });

      generatedHtmlHighlighted.innerHTML = highlightedHtml(messy);
      cleanHtmlOutput.value = cleaned;
      updateStats(messy, cleaned);
    }

    processBtn.addEventListener("click", runProcess);

    copyBtn.addEventListener("click", async () => {
      try {
        await navigator.clipboard.writeText(cleanHtmlOutput.value);
        copyBtn.textContent = "Copied";
        setTimeout(() => {
          copyBtn.textContent = "Copy Clean HTML";
        }, 1400);
      } catch (err) {
        alert("Copy failed. Please copy the clean HTML manually.");
      }
    });

    clearBtn.addEventListener("click", () => {
      plainTextInput.value = "";
      generatedHtmlHighlighted.textContent = "";
      cleanHtmlOutput.value = "";
      updateStats("", "");
    });

    loadSampleBtn.addEventListener("click", () => {
      plainTextInput.value = `Trampilla  Doble para Piso – Receso de 1/8” para Loseta Vinílica/Alfombra\n\nLa PA-BA-FT-8040-DL ofrece una solución de perfil bajo para accesos en pisos terminados con loseta vinílica o alfombra.\n\nConsulta la ficha técnica para datos de carga, herrajes y recomendaciones de instalación.`;
      generatedHtmlHighlighted.textContent = "";
      cleanHtmlOutput.value = "";
      updateStats("", "");
    });
  </script>
</body>
</html>
