$output(settings) =>
  if(!window.input) window.input = {};
  if(!window.t2i) {
    window.t2i = {};
    window.t2i.defaultText = {
      promptTip: `Tip: You can write text like &lcub;blue|red|green&rcub; and a random one will be chosen for each image.\n\nClick the info icon on an image to see the prompt that was used.\n\nAdd brackets around text (like this) or ((even more)) to get the AI to pay more attention to it.`,
      negativePromptTip: `Important: If, for example, you don't want the image to have blue in it, just write "blue" in this box. Don't write "no blue"! In other words, you should write a description of a hypothetical "opposite image" of what you want.`,
      scratchpadTip: `A little notepad for you to keep good prompts and stuff. It 'remembers' the text that you put in it even after you refresh/close this page.\n\nThe text will only be forgotten if you clear your browser's "site data" (cookies, localStorage, etc.) for perchance.org`,
      scratchpadPlaceholder: `Use this box to store prompts and notes. They'll be remembered even if you refresh this page (unless you are in private browsing mode). Also, you can write:\n\nANIMAL = &lcub;dog|mouse|frog&rcub;\n\nin this box, and then write '&lsqb;ANIMAL&rsqb;' within your description (including the square brackets) to get a random one of those animals for each generated image. And you can replace the 🎲 button's outputs by adding a DESCRIPTION line to this text box, like this:\n\nDESCRIPTION = a very &lcub;silly|curious&rcub; &lsqb;ANIMAL&rsqb;`,
    };
    window.t2i.handyText = window.t2i.defaultText; // 'handyText' was the old name
  }

  // Add some default settings (it's okay to write directly to the input parameter, because it's only used once - i.e. this function is only called once)
  if(!settings.imageOptions.style) {
    settings.imageOptions.style = "border-radius:3px;";
  }

  // We need to make these global since we wnat to reference them from the HTML of the *importing* generator (e.g. in click handlers, etc.)
  window.___textToImagePlugin746291937 = textToImagePlugin;
  window.___commentsPlugin746291937 = commentsPlugin;
  window.___userSettings746291937 = settings;
  window.___fullscreenButton893479 = fullscreenButton;

//   let appState;
//   try {
//     appState = JSON.parse(localStorage.appState_nif983gh4tkshdf79834hi);
//   } catch(e) {
//     appState = {};
//   }
  
//   function saveAppState() {
//     localStorage.appState_nif983gh4tkshdf79834hi = JSON.stringify(appState);
//   }


  if(!window.alreadyAddedT2IStuff83927492374) { // this $output function will only get called once, *except* when author is editing their generator, so this if statement/wrapper thing is just to prevent weird stuff for generator authors
    // hide some clutter when in fullscreen:
    document.addEventListener("fullscreenchange", function(e) {
      let fbBtn = feedbackBtn893745ykfuhd;
      let dmBtn = darkModeBtn_875979f948;
      if(document.fullscreenElement) {
        // we just entered fullscreen
        dmBtn.dataset.preFullscreenDisplayValue = dmBtn.style.display;
        dmBtn.dataset.duringFullscreenDisplayValue = "none";
        dmBtn.style.display = "none";
        
        fbBtn.dataset.preFullscreenDisplayValue = fbBtn.style.display;
        fbBtn.dataset.duringFullscreenDisplayValue = "none";
        fbBtn.style.display = "none";
      } else {
        // we just exited fullscreen, so we revert the display values *if* they're the same as what we set them to when we entered fullscreen (so we don't interfere with other code that may have since changed the display values)
        if(dmBtn.style.display === dmBtn.dataset.duringFullscreenDisplayValue) {
          dmBtn.style.display = dmBtn.dataset.preFullscreenDisplayValue;
        }
        delete dmBtn.dataset.preFullscreenDisplayValue;
        delete dmBtn.dataset.duringFullscreenDisplayValue;
        if(fbBtn.style.display === fbBtn.dataset.duringFullscreenDisplayValue) {
          fbBtn.style.display = fbBtn.dataset.preFullscreenDisplayValue;
        }
        delete fbBtn.dataset.preFullscreenDisplayValue;
        delete fbBtn.dataset.duringFullscreenDisplayValue;
      }
    });
    window.alreadyAddedT2IStuff83927492374 = true;
  }

  window.__createSelectorModal_56974639274 = async function(options) { // use it like this: await createSelectorModal([{html:"foo", value:"foo"}, {html:"bar", value:"bar"}])
    let resolve;
    let promise = new Promise(r => resolve = r);

    let ctn = document.createElement("div");
    let modal = document.createElement("div");
    ctn.style.cssText = `position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.3); backdrop-filter: blur(5px); display:flex; align-items:center; justify-content:center;`;
    modal.style.cssText = `max-width:900px; width:95%; max-height:95%; background:var(--box-color); box-shadow:0 0 10px rgba(0,0,0,0.5); overflow:auto;`;

    options.forEach(opt => {
      let item = document.createElement("div");
      item.innerHTML = opt.html;
      item.style.cssText = `margin:0.5rem; border:3px solid ${opt.selected ? "#565656" : "rgba(0,0,0,0)"}; cursor:pointer;`;
      if(!opt.disabled) {
        item.onmouseover = () => item.style.border = "3px dashed #8a8a8a";
        item.onmouseout = () => item.style.border = opt.selected ? "3px solid #565656" : "3px solid rgba(0,0,0,0)";
        item.onclick = () => resolve(opt.value);
      }
      modal.appendChild(item);
    });

    ctn.onclick = (e) => {
      if(e.target === ctn) resolve(null); // returns null if the user didn't select an option (i.e. they clicked somewhere outside the popup)
    };

    ctn.appendChild(modal);
    document.body.append(ctn);

    let value = await promise;
    ctn.remove();
    return value;
  }

  function saveRememberedInput(inputName, inputValue) {
    localStorage[`rememberedInput_P9hkcjnv92hfO2bf9hiu_${inputName}`] = inputValue;
  }
  function getRememberedInput(inputName) {
    return localStorage[`rememberedInput_P9hkcjnv92hfO2bf9hiu_${inputName}`];
  }
  
  function updateInputVisibilities() {
    let userInputs = window.___userSettings746291937.userInputs;
    for(let name of userInputs.getChildNames) {
      if(userInputs[name].visible !== undefined) {
        userInputs[name]._element.closest('.input-ctn').style.display = userInputs[name].visible() ? "" : "none";
      }
    }
  }

  /////////////////////////////////////////////////
  //     handler for <select> element changes    //
  /////////////////////////////////////////////////
  window.___selectElChangeEvent746291937 = function(event) {
    let userInputSpec = window.___userSettings746291937.userInputs[this.dataset.name];    
    let optionsList = userInputSpec.compiledOptions;
    
    let displayName;
    if(this.value.startsWith("ref:optionKeyName:")) {
      // this option wasn't just a simple key=value, it was a node with child properties, so we expose the whole option object/node as a global variable called `this.dataset.name` 
      let key = this.value.split(":")[2];
      window.input[this.dataset.name] = optionsList[key];
      userInputSpec.value = optionsList[key];
      displayName = key;
    } else {
      window.input[this.dataset.name] = this.value;
      userInputSpec.value = this.value;
      displayName = this.value;
    }
    
    let visualSelectBtn = this.parentElement.querySelector(".visual-select-btn");
    if(visualSelectBtn) {
      visualSelectBtn.textContent = displayName;
    }
    
    if(!event._isInitCall) {
      if(userInputSpec.remember) {
        saveRememberedInput(this.dataset.name, this.value);
      }
    }
    
    if(!event._isInitCall) {
      updateInputVisibilities();
    }
  };
  
  ////////////////////////////////////////////////////////////
  //     handler for <input>/<textarea> element changes     //
  ////////////////////////////////////////////////////////////
  let parseVariablesRateLimitTimeout = null;
  let allPreviouslyParsedVariableNames = new Set();
  function parseAllVariables() {
    let variableSourceTextareas = Object.values(window.___userSettings746291937.userInputs).filter(o => o.parseVariables && o._element.tagName.toLowerCase() === "textarea").map(o => o._element);
    for(let name of allPreviouslyParsedVariableNames) {
      delete window[name]; // clear old variables first (if they e.g. delete their scratchpad text, the variables should go away)
    }
    allPreviouslyParsedVariableNames.clear();
    for(let el of variableSourceTextareas) {
      let varLines = el.value.split("\n").filter(line => /^[A-Z0-9_]+? *=.+$/.test(line));
      for(let line of varLines) {
        let name = line.split("=")[0].trim();
        let value = line.split("=").slice(1).join("=");
        window[name] = value.trim();
        allPreviouslyParsedVariableNames.add(name);
      }
    }
  }
  window.___inputElInputEvent746291937 = function(event) {
    let userInputSpec = window.___userSettings746291937.userInputs[this.dataset.name]; 
    
    let value = this.value;
    
    if(value === "" && userInputSpec.defaultValue !== undefined) value = userInputSpec.defaultValue;
        
    if(userInputSpec.parseVariables) {
      clearTimeout(parseVariablesRateLimitTimeout);
      parseVariablesRateLimitTimeout = setTimeout(parseAllVariables, 1000);
    }
    
    window.input[this.dataset.name] = value;
    userInputSpec.value = value;
    
    if(!event._isInitCall) {
      if(userInputSpec.remember) {
        saveRememberedInput(this.dataset.name, value);
      } else if(this.tagName === "TEXTAREA" || (this.tagName === "INPUT" && this.type === "text")) { // <-- just to be sure
        // localStorage["t2i_tempRememberedInput_fisuyr9hf_"+this.dataset.name] = value;
      }
    }
    
    if(!event._isInitCall) {
      updateInputVisibilities();
    }
  };
  (async function() {
    while(!Object.values(window.___userSettings746291937.userInputs)[0]._element) await new Promise(r => setTimeout(r, 500));
    parseAllVariables();
  })();
  
  let socialFeatures = settings.socialFeatures;
  if(socialFeatures === undefined) socialFeatures = "enabled";
  socialFeatures = socialFeatures.evaluateItem;
  
  let thisGeneratorIsUsingClickCountBasedSocialDisplay = false;
  {
    let n = (socialFeatures.match(/button after ([0-9]+) clicks/) || [])[1];
    if(n) {
      thisGeneratorIsUsingClickCountBasedSocialDisplay = true;
      n = Number(n);
      if(Number(localStorage.generateClickCount || 0) >= n) {
        socialFeatures = "button";
      } else {
        socialFeatures = "disabled";
        window.showSocialFeaturesButtonAfterThisManyClicks = n;
      }
    }
  }
  
  
  
  
  
  ////////////////////////////////////////////////////////////
  //            copy share link button handler              //
  ////////////////////////////////////////////////////////////
  window.__generateSettingsShareLink084722 = async function generateShareLink() { 
    
    // EDIT: decided to show this on every click now, since it's obviously not super commonly clicked (so an extra tap is not a big burden), and i think it being close to the generate button means people were clicking it accidentally sometimes.
    // if(!localStorage.knowsHowShareLinksWork) {
    // heuristic to try to warn about their scratchpad being shared too:
    let includingScratchpadWarning = "";
    let sharableScratchpadExists = Object.values(window.___userSettings746291937.userInputs).find(o => (o.getName === "scratchpad" || o.getName === "notes" || o.getName === "notepad") && !o.excludeFromShareLink);
    if(sharableScratchpadExists) includingScratchpadWarning = `, 𝗶𝗻𝗰𝗹𝘂𝗱𝗶𝗻𝗴 the scratchpad/notepad text`;

    let confirmed = confirm(`This will create a sharable link to a snapshot of the settings you've specified above${includingScratchpadWarning}.`);
    if(!confirmed) {
      return;
    }
    localStorage.knowsHowShareLinksWork = "1";
    // }
  
    if(!window.CompressionStream) {
      alert("Share links use a feature that's only available in modern browsers. Please upgrade your browser to the latest version to use this feature.");
      return;
    }
    shareLinkCtn83927376.style.display = "none";

    shareSettingsBtn758374.disabled = true;
    shareSettingsBtn758374.innerHTML = `<span style="display:inline-block; animation:rotate 1.5s linear infinite;">⏳</span> uploading settings data...`;

    let dataJsonStr = JSON.stringify(getAllUserInputValues());

    // convert json text to blob:
    let blob = await fetch("data:text/plain;charset=utf-8,"+dataJsonStr.replace(/#/g, "%23")).then(res => res.blob());

    // compress blob:
    let compressedBlob = await compressBlobWithGzip(blob);

    let { url, size, error } = await upload(compressedBlob);
    if(error) {
      shareSettingsLinkInputEl759307.value = `error: ${error}`;
      alert(`Error: ${error}${error === "disallowed_content" ? ". If you believe this is incorrect, then you may need to explicitly state that relevant characters are 18 or older, since the moderation system can make mistakes if there is ambiguity." : ""}`);
    } else {
      shareSettingsLinkInputEl759307.value = `https://perchance.org/${window.generatorName}#data=`+url.replace("https://user.uploads.dev/file/", "uup1:");
    }
    shareLinkCtn83927376.style.display = "";
    shareSettingsBtn758374.textContent = "🔗 share these settings";
    shareSettingsBtn758374.disabled = false;
    shareSettingsBtn758374.style.display = "none";
  }
  
  async function compressBlobWithGzip(blob) {
    const cs = new CompressionStream('gzip');
    const compressedStream = blob.stream().pipeThrough(cs);
    let outputBlob = await new Response(compressedStream).blob();
    return new Blob([outputBlob], { type: "application/gzip" }); // <-- to add the correct mime type
  }
  
  async function loadDataFromUrlHash() { 
    let success = false;
    if(!window.DecompressionStream) {
      alert("Share links use a browser feature that's only available in modern browsers. Please upgrade your browser to the latest version (ideally Chrome or Edge) to allow for loading data from character share links.");
      return {success, error:"browser_compat"};
    }

    let loadingModal = document.createElement('div');
    loadingModal.innerHTML = `<div style="position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.5); z-index: 9999; display: flex; justify-content: center; align-items: center;">
      <div style="padding: 20px; background-color: var(--box-color); border-radius: 8px;">
        <span style="display:inline-block; animation:rotate 1.5s linear infinite;">⏳</span> loading share link data...
      </div>
    </div>`;
    loadingModal = loadingModal.firstElementChild;
    document.body.append(loadingModal);

    try {
      let hashText = window.location.hash.slice(1);
      if(!hashText.startsWith("data=")) {
        throw new Error("Invalid share URL.");
      }
      let fileUrl = hashText.replace(/^data=/, "");
      if(fileUrl.startsWith("uup1:")) {
        fileUrl = fileUrl.replace("uup1:", "https://user.uploads.dev/file/");
      }
      let fetchOptions = {};
      if(window.AbortSignal && AbortSignal.timeout) fetchOptions.signal = AbortSignal.timeout(10000);
      let blob = await fetch(fileUrl, fetchOptions).then(res => res.ok ? res.blob() : null).catch(console.error);
      if(!blob) {
        loadingModal.remove();
        await confirmAsync(`It seems you've tried to load a share URL, but the file specified by the URL does not exist. If you believe it should exist, you can ask for help on the community forum, or check if the file has been quarantined:\n\nperchance.org/quarantined-files`, {hideCancel:true});
        return {success:false, error:"loading_cancelled_by_user"};
      }
      let text;
      if(fileUrl.endsWith(".gz")) {
        let decompressedBlob = await decompressBlobWithGzip(blob);
        text = await decompressedBlob.text();
      } else {
        text = await blob.text();
      }
      let data = JSON.parse(text);
      if(data.___format === "t2i-framework-format1") {
        let confirmed = await confirmAsync("𝗛𝗲𝗮𝗱𝘀 𝘂𝗽: You're loading a share link. This will overwrite your existing saved settings/scratchpad, if any. 𝗖𝗼𝗻𝘁𝗶𝗻𝘂𝗲?\n\n(Note: You can click cancel and then load the share link in your browser's incognito/private mode to avoid overwriting any existing data, or just backup your existing data first.)");
        if(!confirmed) {
          window.location.hash = "";
          loadingModal.remove();
          return {success, error:"loading_cancelled_by_user"};
        }
        // let userHasSomeTextTheyAreTryingToRemember = Object.values(window.___userSettings746291937.userInputs).filter(o => o.remember && o._element.tagName.toLowerCase() === "textarea").map(o => o._element.value).join("").length > 100;
        // if(userHasSomeTextTheyAreTryingToRemember) {}
        importUserInputValues(data);
        success = true;
      } else {
        alert("Unknown share link data format.");
      }
    } catch(e) {
      alert(`Failed to load share link data: ${e.message}`);
      console.error(e);
    }

    loadingModal.remove();
    return {success};
  }

  async function decompressBlobWithGzip(blob) {
    const ds = new DecompressionStream("gzip");
    const decompressedStream = blob.stream().pipeThrough(ds);
    return await new Response(decompressedStream).blob();
  }
  
  function getAllUserInputValues() {
    let userInputs = {};
    for(let key in window.___userSettings746291937.userInputs) {
      if(window.___userSettings746291937.userInputs[key].excludeFromShareLink) continue;
      let value = window.___userSettings746291937.userInputs[key]._element?.value;
      if(value !== undefined) {
        userInputs[key] = value;
      }
    }
    userInputs.___format = "t2i-framework-format1";
    return userInputs;
  }
  
  function importUserInputValues(data) {
    for(let key in window.___userSettings746291937.userInputs) {
      if(window.___userSettings746291937.userInputs[key].excludeFromShareLink) continue;
      let value = window.___userSettings746291937.userInputs[key]._element?.value;
      if(value !== undefined) {
        if(data[key] !== undefined) {
          let el = window.___userSettings746291937.userInputs[key]._element;
          el.value = data[key];
          if(el.onchange) el.onchange({_isInitCall:true});
          if(el.oninput) el.oninput({_isInitCall:true});
        }
      }
    }
  }
  
  
  
  
  
  
  
  
  function animateImageIntoTarget({imgEl, sourceEl, targetEl, durationMs, wiggle}={}) {
    const srcRect = sourceEl.getBoundingClientRect()
    let tgtRect = targetEl.getBoundingClientRect();
    imgEl.style.cssText = `position:fixed;top:${srcRect.top}px;left:${srcRect.left}px;width:${srcRect.width}px;height:${srcRect.height}px;margin:0;pointer-events:none;z-index:9999;transition:top ${durationMs}ms cubic-bezier(.4,0,.2,1),left ${durationMs}ms cubic-bezier(.4,0,.2,1),width ${durationMs}ms cubic-bezier(.4,0,.2,1),height ${durationMs}ms cubic-bezier(.4,0,.2,1)`;
    if (!imgEl.parentNode) document.body.appendChild(imgEl);
    imgEl.getBoundingClientRect(); // force reflow
    imgEl.style.top = tgtRect.top + tgtRect.height / 2 + 'px';
    imgEl.style.left = tgtRect.left + tgtRect.width / 2 + 'px';
    imgEl.style.width = imgEl.style.height = '0px';
    
    imgEl.addEventListener('transitionend', () => {
      imgEl.remove();
      if (!wiggle) return;
      const anim = 'wiggle' + Math.random().toString(36).slice(2);
      const styleEl = Object.assign(document.createElement('style'), {
        id: anim,
        textContent: `@keyframes ${anim}{0%,100%{transform:rotate(0deg)}20%{transform:rotate(-5deg)}40%{transform:rotate(5deg)}60%{transform:rotate(-3deg)}80%{transform:rotate(3deg)}}`
      });
      document.head.appendChild(styleEl);
      targetEl.style.animation = `${anim} 400ms ease-in-out`;
      targetEl.addEventListener('animationend', () => { targetEl.style.animation = ''; styleEl.remove(); }, { once: true });
    }, { once: true });
  }


  if(settings.imageButtons?.privateSave) {
    window.t2i_privateGallery = createMediaGalleryPlugin();
    document.addEventListener("keydown", e => {
      if (e.shiftKey && (e.ctrlKey || e.metaKey) && e.key.toLowerCase() === "g") {
        e.preventDefault();
        window.t2i_privateGallery.show();
      }
    });
  }
  
  window.t2i_privateGallerySave = async function(btn, imageCtn) {
    try {
      if(!imageCtn.querySelector("iframe").textToImagePluginOutput) return alert("Please wait for the image to finish generating before using the 'private save' feature.");
      btn.disabled = true;
      let textToImagePluginOutput = imageCtn.querySelector("iframe").textToImagePluginOutput;
      let { prompt, negativePrompt, seed, guidanceScale } = textToImagePluginOutput.inputs;
      let imageBlob = await fetch(textToImagePluginOutput.dataUrl).then(r => r.blob());
      await window.t2i_privateGallery.add(imageBlob, {info:{prompt, negativePrompt, seed, guidanceScale}});
      
      privateGalleryCtn97364827.hidden = false;
      
      // Animate the image into gallery button:
      let imgEl = document.createElement("img");
      imgEl.src = textToImagePluginOutput.dataUrl;
      animateImageIntoTarget({imgEl, sourceEl:imageCtn.querySelector("iframe"), targetEl:privateGalleryCtn97364827.querySelector("button"), durationMs:700, wiggle:true});
    } catch(e) {
      console.error(e);
    }
    setTimeout(() => {
      btn.disabled = false; // we re-enable since they can click the (within-iframe) 'regenerate' button on this image and submit another image
    }, 1000);
  };
  window.t2i_openCharacterDescriptionEditor = function(imageCtn) {
    imageCtn.querySelector('.personality-chat-button').style.visibility = 'hidden';
    if(imageCtn.querySelector('.char-description-editor-ctn')) {
      // show it existing on if it exists:
      imageCtn.querySelector('.char-description-editor-ctn').style.display = 'flex';
    } else {
      // otherwise create it:
      let tmp = document.createElement("div");
      tmp.innerHTML = `<div class="char-description-editor-ctn" style="color-scheme:dark; color:#dddddd; position:absolute;top:0;left:0;right:0;bottom:0;display: flex;flex-direction:column; gap:0.125rem; padding:0.125rem; padding-bottom:0; backdrop-filter:brightness(0.7);">
        <!--<div style="font-size:80%; font-weight:bold; text-shadow:0px 0px 8px black, 0px 0px 8px black;">Instructions:</div>-->
        <textarea class="char-description-instruction" style="backdrop-filter:blur(3px); background:rgba(0, 0, 0, 0.8); font-size:80%; min-height:70px; border:1px solid grey; border-radius:2px; color-scheme:dark;" placeholder="(Optional) Extra instructions for the personality, appearance, and backstory to be generated."></textarea>
        <div style="display:flex; gap:0.125rem; justify-content:center;">
          <button class="char-description-generate-button" style="color-scheme:dark; flex-grow:1; font-weight:bold; max-width:max-content; box-shadow:0px 0px 12px 2px black;" onclick="window.t2i_generateCharacterDescription(this.closest('.t2i-image-ctn'))">✨ generate persona</button>
          <button class="char-description-stop-button" style="color-scheme:dark; min-width:4rem; display:none; box-shadow:0px 0px 12px 2px black;" onclick="this.closest('.t2i-image-ctn').__generateObj.stop()">🛑 stop</button>
        </div>
        <!--<div style="font-size:80%; font-weight:bold; text-shadow:0px 0px 8px black, 0px 0px 8px black;">Output:</div>-->
        <!--<div style="">⬇️ ⬇️ ⬇️</div>-->
        <input class="character-name-output" style="backdrop-filter:blur(3px); background:rgba(0, 0, 0, 0.8); border:1px solid grey; border-radius:2px; color-scheme:dark;" placeholder="Character name">
        <textarea class="character-description-output" placeholder="Click the 𝗴𝗲𝗻𝗲𝗿𝗮𝘁𝗲 button above! The character description will appear here, and then you can edit it." style="backdrop-filter:blur(3px); flex-grow:1; background:rgba(0,0,0,0.8); border:1px solid grey; border-radius:2px; font-size:85%; color-scheme:dark;"></textarea>
        <div style="display:flex; gap:0.25rem;">
          <button style="flex-grow:1; color-scheme:dark;" onclick="window.t2i_generateChatLink(this.closest('.t2i-image-ctn'))">💬 chat with them</button>
          <button style="min-width:4rem; color-scheme:dark;" onclick="this.closest('.char-description-editor-ctn').style.display='none'; this.closest('.t2i-image-ctn').querySelector('.personality-chat-button').style.visibility='visible';">❌ close</button>
        </div>
      </div>`;
      imageCtn.appendChild(tmp.firstElementChild);
    }
  };

  window.t2i_generateCharacterDescription = async function(imageCtn) {
  
    let generateBtn = imageCtn.querySelector(".char-description-generate-button");
    let stopBtn = imageCtn.querySelector(".char-description-stop-button");
  
    let originalGenerateButtonTextContent = generateBtn.textContent;
    generateBtn.innerHTML = `<span style="display:inline-block; animation:rotate 1.5s linear infinite;">⏳</span> loading...`;
    generateBtn.disabled = true;
    stopBtn.style.display = "";
  
    while(!imageCtn.querySelector("iframe").textToImagePluginOutput) await new Promise(r => setTimeout(r, 1000)); // bit of a hacky patch, just to cover edge case where they can click the personality button before image generation finishes
  
    let charDescriptionInstruction = imageCtn.querySelector(".char-description-instruction").value.trim();
    let textToImagePluginOutput = imageCtn.querySelector("iframe").textToImagePluginOutput;
    let nameOutputEl = imageCtn.querySelector(".character-name-output");
    let descriptionOutputEl = imageCtn.querySelector(".character-description-output");

    nameOutputEl.value = "";
    descriptionOutputEl.value = "";

    let generateObj;
    if(true) {
      generateObj = aiText({
        instruction: `
        TASK: The user has created an image of a character using a prompt. The prompt is shown below. Your task is to use the character personality hints in the prompt to generate a character description for roleplaying with. Your description should ideally include personality, appearance, speech examples/mannerisms, etc. such that an actor could read it and know how to roleplay as the character in all relevant aspects. Use an information-dense writing style to *succinctly* capture all aspects of the personality of the character that you generate. Your description should be between 200 and 600 words in length. Do your best to generate a character based on what you see below. Follow the hints in the user's image prompt to accurately capture the details that are most important to a character chat/roleplay.

        --- START OF IMAGE PROMPT ---
        ${textToImagePluginOutput.inputs.prompt.replace(/\s+/g, " ")}
        --- END OF IMAGE PROMPT ---

        Reminder: Your task is to generate a character that is inspired by the above image prompt. Your response should describe how the character looks/acts/speaks/behaves such that if someone read it, they would be able to accurately emulate or roleplay as the target character's mannerisms, personality, etc. No more than 600 words.
        ${charDescriptionInstruction ? `\nBut also, the character must be based on this instruction: ${charDescriptionInstruction}\n` : ""}

        Use this template for your response:
        <response_template>
        # Name:
        (The character's name)

        # Visual Description:
        (A SHORT paragraph describing what they look like - keep this short, concise and to-the-point. Low word count, high information density. Avoid using purple prose and overly flowery descriptions.)

        # Personality Description:
        (ONE paragraph personality description. Use mainly short sentences - i.e. write in an information-dense style. Focus on the aspects that make them unique/interesting. If relevant, you can use terms like tsundere, yandere, kuudere, deredere, dandere, genki girl, kuudere, seme, uke, megane, chuunibyou, clumsy heroine, superiority complex, dominant, submissive, etc. to succinctly capture aspects of their personality. Invent a few interesting aspects, use creative liberty to create a character that has genuine depth, with idiosyncrasies, goals, fears, likes, dislikes, ethics, etc. Remember, your goal is to create a character that would be fun for the user to roleplay with! Follow the user's lead based on their initial idea, but take it one step further to surprise and excite them.)

        # Roleplay Behavior Examples:
        (A numbered list of 5 separate and diverse examples of character speech/behavior, each starting with an asterisk or double-quote, which place the character in random roleplay situations, as if you've extracted random & diverse moments from a story, with sufficient context captured by the dialogue and actions themselves, to show the essence of who they are as a character, how they speak/act/react/etc. Each example should perfectly capture one aspect of who they are as a character. Each example should be its own separate momement from a hypothetical story, unrelated to the other examples that you write. You must use asterisks around actions and quotes around speech in typical roleplays style, for example [don't use this specific example in your response, it's just to demonstrate the syntax]: 1. "And why the..." *He gestures to the overturned carriage* "...grand entrance?" [...])
        
        # Favorite Food:
        (favorite food)
        </response_template>
        `.trim(),
        startWith: "# Name:",
        stopSequences: ["# Favorite"],
        onChunk: (data) => {
          let parts = data.fullTextSoFar.split(/(^|\n\n)# /s).map(p => p.trim()).filter(p => p);
          let name = parts.find(p => p.startsWith("Name"))?.split("\n")?.[1]?.trim();
          let visualDescription = parts.find(p => p.startsWith("Visual"))?.split("\n").slice(1).join("\n").trim() || "";
          let personalityDescription = parts.find(p => p.startsWith("Personality"))?.split("\n").slice(1).join("\n").trim() || "";
          let roleplayBehaviorExamples = parts.find(p => p.startsWith("Roleplay"))?.split("\n").slice(1).join("\n").trim() || "";

          nameOutputEl.value = name || "";

          let description = `# {{char}} Visual Description:\n${visualDescription}\n\n# {{char}} Personality:\n${personalityDescription}\n\n# {{char}} Roleplay Behavior Examples:\n${roleplayBehaviorExamples}`;
          descriptionOutputEl.value = description;

          if(descriptionOutputEl.scrollTop > (descriptionOutputEl.scrollHeight - descriptionOutputEl.offsetHeight)-30) { // <-- if the text box is already scrolled near the end of the text
            descriptionOutputEl.scrollTop = 99999999; // scroll down to bottom of textarea as text streams in
          }
        },
      });
    } else {
      // generateObj = aiText({
      //   instruction: `
      //   Your task is to invent a character description for a roleplay.
      //   All you know about the character is what you can glean from this image prompt: ${textToImagePluginOutput.inputs.prompt.replace(/\s+/g, " ")}
      //   ${charDescriptionInstruction ? "But also, the character must be based on this instruction: "+charDescriptionInstruction : ""}
      //   Your response should use this template:
      //   ---
      //   Name: <character's name>
      //   Personality: <one paragraph summarizing their personality, including specific interesting quirks>
      //   Background: <one paragraph summarizing their backstory>
      //   Physical Features (IGNORING ATTIRE/POSE): <up to 10 comma-separated keywords/phrases indicating *permanent* physical features like body type/build/size, skin color, eye color, hair color, nose shape, etc>
      //   Age: <their age>
      //   ---
      //   Respond with the above template, loosely inspired by details from the image description. Create an engaging, captivating, and *genuinely fascinating* character. Keep your response short. Use lean, unpretentious, crisp, descriptive words that vividly and *concisely* capture the most interesting aspects of the character that you've invented.
      //   IMPORTANT: Do NOT describe the image. This isn't about the image. It's about the *character*. Talk about the character, not the image. The image just provides clues.
      //   `.trim(),
      //   startWith: "Name:",
      //   stopSequences: ["Age:"],
      //   onChunk: (data) => {
      //     let parts = data.fullTextSoFar.split(/(Name:.*)/);
      //     nameOutputEl.value = parts[1].trim().replace(/^Name:/, "").trim();
      //     descriptionOutputEl.value = parts[2].trim().replace(/\nAge:.?$/, "").replace(" (IGNORING ATTIRE/POSE)", "");
      //     if(descriptionOutputEl.scrollTop > (descriptionOutputEl.scrollHeight - descriptionOutputEl.offsetHeight)-30) { // <-- if the text box is already scrolled near the end of the text
      //       descriptionOutputEl.scrollTop = 99999999; // scroll down to bottom of textarea as text streams in
      //     }
      //   },
      // });
    }
    
    imageCtn.__generateObj = generateObj; // so the stop button can access it
    let data = await generateObj;

    generateBtn.textContent = originalGenerateButtonTextContent;
    generateBtn.disabled = false;
    stopBtn.style.display = "none";
  };

  window.t2i_generateChatLink = async function(imageCtn) {
    let name = imageCtn.querySelector(".character-name-output").value.trim().replace(/\n+/g, " ");
    let firstName = name.split(/[ ,]/)[0].trim();
    let description = imageCtn.querySelector(".character-description-output").value.trim().replace(/\n+/g, "\n").trim();
    let textToImagePluginOutput = imageCtn.querySelector("iframe").textToImagePluginOutput;

    if(!description) return alert("First click the 𝗴𝗲𝗻𝗲𝗿𝗮𝘁𝗲 𝗽𝗲𝗿𝘀𝗼𝗻𝗮 button above to generate a character description.");

    let appearance = (((description+"\n").match(/Physical Features *(|\(.+?\)): (.+?)\n/s) || [])[1] || "").trim();

    let roleInstruction = `
    # Description of ${name}:
    ${description}

    # Roleplay Guidelines:
    - Bring characters to life by realistically portraying their unique traits, thoughts, emotions, appearances, physical sensations, speech patterns, and tone. Consider the situation, characters' motivations, and potential consequences.
    - Ensure that their reactions, interactions, and decision-making align with their established personalities/descriptions, values, goals, and fears. You must **ALWAYS** maintain accurate character portrayals.

    Creatively improvise this fictional roleplay between {{char}} and {{user}} to create an interesting and engaging experience/story/chat, no matter where {{user}} decides to lead it. The overall goal is to create a genuinely fascinating and engaging roleplay/story. So good that you can't stop reading. Be proactive, introducing new events, proposing new conversation topics, and leading the role-play in new, interesting directions when appropriate to keep the overall experience engaging.
    `.trim();

    let characterImagePrompt = textToImagePluginOutput.inputs.prompt.replace(/\s+/g, " ");
    let characterImageDataUrl = textToImagePluginOutput.dataUrl;
    
    function addStyleToPrompt(styleName, prompt) {
      // bit weird, but we use a global to 'communicate' with the t2i-styles generator's scope.
      let originalWindowInput = window.input;
      window.input = {description:prompt};
      let result = t2iStyles[styleName].prompt.evaluateItem;
      window.input = originalWindowInput;
      return result;
    }

    function addStyleToNegative(styleName, negative) {
      // bit weird, but we use a global to 'communicate' with the t2i-styles generator's scope.
      let originalWindowInput = window.input;
      window.input = {negative};
      let result = t2iStyles[styleName].negative.evaluateItem;
      window.input = originalWindowInput;
      return result;
    }
    
    // If the generator has an artStyle select element, then we assume it's t2iStyles, and we use an empty prompt to extract only the style, so that style can be used in the chat by default
    let imagePromptSuffix = "";
    try { // try/catch because new code
      let artStyleName = window.___userSettings746291937.userInputs.artStyle?._element.value.split(":")[2];
      if(artStyleName) {
        // we remove stuff like "(:1.3)" from the prompt because the style expects some input text, but we're not giving it here:
        let prompt = addStyleToPrompt(artStyleName, "").replace(/\([():.0-9 ]*\)/g, "").replace(/\s+/g, " ");
        imagePromptSuffix = `${prompt} (negativePrompt:::${addStyleToNegative(artStyleName, "")}) (resolution:::512x768)`;
      }
    } catch(e) {};

    let json = {
      addCharacter: {
        name,
        roleInstruction,
        maxParagraphCountPerMessage: 2, // TODO: add a drop-down selector for this?
        imagePromptSuffix,
        // I've commented out the line below for now because I've updated the character gen prompt, and it doesn't currently have a "purely appearance-only, without reference to pose/clothes/etc." section
        // imagePromptTriggers: `${firstName}: ${firstName}'s appearance: ${appearance || characterImagePrompt}`, // better to use `appearance` (if we have it) rather than `characterImagePrompt`, because character can be in images with other characters, but `characterImagePrompt` would likely affect overall image too much, since it's designed to generate a "whole" image.
        initialMessages: [
          {author:"system", content:`<i style="font-size:85%;">Introduce yourself to ${name}, or perhaps <b style="color:#00af00;">tap the Narrator button</b> and tell it to generate an initial roleplay scenario based on some keywords/themes. You can change your name using the 'options' button.</i>`, hiddenFrom:["ai"]},
        ],
        avatar: {url:characterImageDataUrl, size:1, shape:"square"},
      },
      quickAdd: true,
    };
    await window.t2i_generateShareLinkForCharacter(json);
  };

  window.t2i_generateShareLinkForCharacter = async function(json) {
    if(!window.CompressionStream) {
      alert("Character chat links use a feature that's only available in modern browsers. Please upgrade your browser to the latest version to use this feature. If you're using Safari, switch to Chrome instead.");
      return;
    }

    let loadingModal = window.t2i_createLoadingModal("⌛ Generating chat link...");

    let jsonString = JSON.stringify(json); 

    let urlHashData = encodeURIComponent(JSON.stringify(json)).replace(/[!'()*]/g, function(c) {
      return '%' + c.charCodeAt(0).toString(16); // since encodeURIComponent doesn't encode some characters (like parentheses) and they mess up markdown links
    });
    const shareUrlHashVersion = `https://perchance.org/ai-character-chat#${urlHashData}`;
    console.log("shareUrl (hash version):", shareUrlHashVersion);

    // convert json text to blob:
    let dataUrlJsonString = jsonString.replace(/#/g, "%23"); // since hash is a special character in dataurls (like normal URLs)
    let blob = await fetch("data:text/plain;charset=utf-8,"+dataUrlJsonString).then(res => res.blob());

    // compress blob:
    let compressedBlob = await compressBlobWithGzip(blob);

    let { url, size, error, deletionUrl } = await upload(compressedBlob);
    if(error) {
      loadingModal.delete();
      // Originally checked to see if moderation error, but I think too many false positives atm, so just using hash version so no upload is required.
      // Could also just be that upload plugin is having issues of course, or blocked due to e.g. using a VPN where other users have exhausted the daily limit for that VPN IP
      await prompt2({
        content: {type:"none", html:`
          <div style="margin-bottom:0.5rem;opacity:0.7;font-size:90%;text-align: center;">Your character has been created. Click to chat:</div>
          <button onclick="window.open('${shareUrlHashVersion}')" style="min-width:max-content;display: block;margin: 1rem auto;font-size: 1.3rem;padding: 0.5rem;">↗️ open chat in new tab 💬</button>
          <p style="font-size:75%; opacity:0.6; text-align:center;">Note: A sharable link could not be created due to an upload error. Using a VPN may cause you to hit upload limits faster. Also, the moderation system may reject uploads if there are adult themes and it's not clear that relevant characters are 18 or older, in which case you should explicitly state character ages.</p>
        `},
      }, {colorScheme: "dark", cancelButtonText:null, submitButtonText:"❌ finished", verticallyCenter:true});
    } else {
      loadingModal.delete(); 
      let fileName = url.replace("https://user.uploads.dev/file/", "");
      let characterName = json.addCharacter.name.replace(/\s+/g, "_").replaceAll("~", "").replaceAll(`"`, ""); // this is just so URL is more readable - doesn't affect stored data at all
      let shareUrl = `https://perchance.org/ai-character-chat?data=${characterName}~${fileName}`;
      console.log("shareUrl:", shareUrl);

      let alreadyDeleting = false;
      window.__deleteShareUrlHandler4738472929 = async function() {
        if(!confirm("This share URL will be permanently deleted. Note that deletion may not be instant (due to caching). Continue?")) return;
        if(alreadyDeleting) return;
        alreadyDeleting = true;
        deleteShareUrlCtn.style.opacity = "0.5";
        deleteShareUrlCtn.textContent = "⏳ Deleting...";
        let { success } = await fetch(deletionUrl).then(r => r.json());
        deleteShareUrlCtn.textContent = "✅ Deleted.";
      };

      let colorScheme = "dark"; //localStorage.forceColorScheme !== undefined ? localStorage.forceColorScheme : window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches ? "dark" : "light";
      await prompt2({
        content: {type:"none", html:`
          <div style="margin-bottom:0.5rem; opacity:0.7; font-size:90%;">Your character has been created. Here's the link:</div><div style="display:flex; gap:0.5rem;"><input value="${shareUrl}" style="flex-grow:1; color-scheme:${colorScheme}; min-width:0; font-size:80%;"> <button onclick="navigator.clipboard.writeText(this.parentElement.querySelector('input').value); this.textContent='copied ✅'; setTimeout(() => { this.textContent='📋 copy'; }, 2000);" style="min-width:max-content;">📋 copy</button> <button onclick="window.open(this.parentElement.querySelector('input').value)" style="min-width:max-content;">↗️ visit</button> </div>
          <div id="deleteShareUrlCtn" style="font-size:80%;margin-top: 0.5rem;">You can <span onclick="window.__deleteShareUrlHandler4738472929()" style="color:red;text-decoration:underline;cursor: pointer;">click here</span> to delete this share URL.</div>
        `},
      }, {colorScheme, cancelButtonText:null, submitButtonText:"finished", verticallyCenter:true});
    }
  }

  window.t2i_createLoadingModal = function(initialContent, parentElement) {
    if(!parentElement) parentElement = document.body;
    let loadingModalCtn = document.createElement("div");
    loadingModalCtn.innerHTML = `<style>
      .loadingModalCtn-856246272937 {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0,0,0,0.7);
        color-scheme: dark;
        color:#e7e7e7;
        z-index: 100;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 1rem;
        z-index: 99999999;
      }
      .loadingModalContent-856246272937 {
        background-color: #151515;
        border-radius: 3px;
        padding: 1rem;
        text-align: center;
        box-shadow: 0px 1px 10px 3px rgb(130 130 130 / 24%);
      }
    </style>`;
    let contentEl = document.createElement("div");
    contentEl.classList.add("loadingModalContent-856246272937");
    contentEl.innerHTML = initialContent || "";
    loadingModalCtn.appendChild(contentEl);
    loadingModalCtn.classList.add("loadingModalCtn-856246272937");
    parentElement.appendChild(loadingModalCtn);
    return {
      updateContent: function(content) {
        contentEl.innerHTML = content;
      },
      delete: function() {
        loadingModalCtn.remove();
      },
    }
  }
  
  
  
  
  
  
  
  
  
  ///////////////////////////////////////////////
  //        social/gallery button stuff        //
  ///////////////////////////////////////////////
  window.__showSocialsButtonClickHandler98347938 = function() {
    commentsPluginCtn654732.appendChild(window.generateCommentsPluginElement85638294());
    imageGalleryCtn8569284.innerHTML = window.generateImageGalleryHtml358402048();
    showHideGalleryAndCommentsButtonsCtn83728732671.style.display = '';
    if(autoShowCheckbox84937483.checked) { localStorage.autoShowSocialsNextTime = '1'; };
    showSocialsButtonCtn9000274628.remove();
    if(thisGeneratorIsUsingClickCountBasedSocialDisplay && !localStorage.theyKnowAboutClickCountBasedSocialDisplay) {
      clickCountBasedSocialDisplayNotifierEl.style.display = "";
    }
  }
  
  
  
  
  ///////////////////////////////////////////////
  //     handler for generate button click     //
  ///////////////////////////////////////////////
  window.___generateButtonClickEvent746291937 = async function(event) {
  
    clearPerchanceErrors(); // in case they has errors in their syntax in their last attempt, we don't want to keep showing the error bubble forever
  
    shareSettingsBtn758374.style.display = "";
    shareLinkCtn83927376.style.display = "none";
  
    generateButtonEl.textContent = "⬇️ generating... ⬇️";
    generateButtonEl.disabled = true;
    generateButtonEl.style.opacity = 0.7;
    setTimeout(() => {
      generateButtonEl.textContent = "✨ generate";
      generateButtonEl.disabled = false;
      generateButtonEl.style.opacity = 1;
    }, 3000);
  
    let numImages = 6;
    if(!isNaN(Number(settings.numImages?.evaluateItem))) numImages = Number(settings.numImages?.evaluateItem);
    if(isNaN(numImages)) numImages = 6;
    
    if(numImages >= 12) {
      mainColumnEl85394739.style.maxWidth = "100%"; // it's set to 1000px by default so 3, 6, 9, and 12 images display nicely as 3 per row, but at 12 or more on wide screens it's fine to take up the full screen width
    } else {
      mainColumnEl85394739.style.maxWidth = "1000px";
    }
    
    if(!window.___userSettings746291937.imageButtons) window.___userSettings746291937.imageButtons = {};
    
    let underEachImageParts = [];
    if(window.___userSettings746291937.imageButtons.personality) underEachImageParts.push(`<button class='personality-chat-button' onclick="window.t2i_openCharacterDescriptionEditor(this.closest('.t2i-image-ctn'))">🎭 persona / 💬 chat</button>`);
    if(window.___userSettings746291937.imageButtons.privateSave) underEachImageParts.push(`<button class='private-save-button' style="margin-left:0.5rem;" title="Private save: Save this image to your browser's local storage" onclick="window.t2i_privateGallerySave(this, this.closest('.t2i-image-ctn'))">🛡️💾</button>`);
    
    let underEachImageHtml = `<div>${underEachImageParts.join("")}</div>`;
    
    if(localStorage.forceColorScheme) window.___userSettings746291937.imageOptions.forceColorScheme = localStorage.forceColorScheme;
    outputAreaEl.innerHTML = `<div class="t2i-image-ctn" style="margin:0.25rem; ${underEachImageParts.length > 0 ? "margin-bottom:0.6rem;" : ""}; position:relative;">[window.___textToImagePlugin746291937(window.___userSettings746291937.imageOptions)]<div class="t2i-under-each-image-ctn">${underEachImageHtml}</div></div>`.repeat(numImages).evaluateItem + (settings.underImagesMessage ? `<div style="margin-top:0.5rem;flex:0 0 100%">${settings.underImagesMessage}</div>` : "");
    
    let generateClickCount = Number(localStorage.generateClickCount || 0);
    generateClickCount++;
    if(isNaN(generateClickCount) || typeof generateClickCount !== "number") generateClickCount = 1;
    localStorage.generateClickCount = generateClickCount;
    
    if(window.showSocialFeaturesButtonAfterThisManyClicks !== undefined && generateClickCount >= window.showSocialFeaturesButtonAfterThisManyClicks) {
      let socialButton = document.querySelector("#showSocialsButtonCtn9000274628");
      if(socialButton) socialButton.style.display = "";
    }
    
    if(generateClickCount > 3) {
      shareLinkOuterCtn2893827.style.display = "";
    }
    if(generateClickCount > 3) {
      pageTitleEl48759084793.style.display = "none";
    }
    
    if(generateClickCount > 9 && generateClickCount%10 === 0) { // caution: don't change %10 without changing the alert condition below
      let successfullyPersisting = false;
      let errorWhileTryingToPersist = false;
      try {
        if(navigator.storage && navigator.storage.persist) {
          let persistent = await navigator.storage.persist();
          if(persistent) {
            console.log("Storage will not be cleared except by explicit user action");
            successfullyPersisting = true;
          } else {
            console.log("Storage may be cleared by the UA under storage pressure.");
          }
        }
      } catch(e) {
        errorWhileTryingToPersist = e;
        console.error(e);
      }
      if(!successfullyPersisting) {
        try {
          let userHasSomeTextTheyAreTryingToRemember = Object.values(window.___userSettings746291937.userInputs).filter(o => o.remember && o._element.tagName.toLowerCase() === "textarea").map(o => o._element.value).join("").length > 100;
          if(userHasSomeTextTheyAreTryingToRemember) {
            // note: these numbers must match generateClickCount%10
            if(generateClickCount === 20 || generateClickCount === 50 || generateClickCount === 100 || generateClickCount === 200 || generateClickCount === 400 || generateClickCount === 800) {
              alert(`Heads up: Your browser is currently not allowing scratchpad/input text to be permanently stored/persisted between page visits. The stored text may eventually be deleted by your browser. This could be because you're in incognito/private mode, or your hard drive is nearly full, or because the web browser you're using has bugs. Chrome is currently best browser in my experience (fewest bugs).`);
            }
          }
        } catch(e) { console.error(e); }
      }
    }
    
    updateInputVisibilities();
    
    bottomClickHelperEl.style.display = "none";
    leftClickHelperEl.style.display = "none";
    rightClickHelperEl.style.display = "none";
  };
  
  function generateModifierHtml(modifiers) {
    let selectEls = [];
    for(let modifierName of modifiers.getAllKeys) {
      let list = modifiers[modifierName];
      if(!list.getAllKeys) continue; // some people have bugs in their list indentation etc. and this prevents their bugs from breaking the other modifiers
      let optionEls = [];
      optionEls.push(`<option value="" selected>Add ${modifierName}...</option>`);
      for(let key of list.getAllKeys) {
        optionEls.push(`<option value="${list[key].evaluateItem.replaceAll("\"", "&quot;")}">${key}</option>`)
      }
      selectEls.push(`<select onchange="if(this.value) { this.closest('.input-ctn').querySelector('textarea').value += this.value; this.value=''; this.closest('.input-ctn').querySelector('textarea').oninput({}); let o=this.querySelector('option'); let originalFirstItemContent=o.textContent; o.textContent='↑ Added ↑'; setTimeout(() => o.textContent=originalFirstItemContent, 1000); }" style="margin-right:0.25rem; margin-top:0.25rem; font-size:80%;">${optionEls.join("")}</select>`);
    }
    return selectEls.join("");
  }
  
  let initHandlers = [];

  ////////////////////////////////////////////////////////////////////////////
  //     generate the HTML based on the specified userInputs list items     //
  ////////////////////////////////////////////////////////////////////////////
  let userInputsHtml = settings.userInputs.selectAll.map(item => {
  
    let tipText = item.tip?.evaluateItem.trim();
    if(tipText) tipText = tipText.replace(/"/g, "&quot;");
  
    if(item.type === "text") {
      let widthStyle = "";
      if(item.width) widthStyle += `width:${item.width};`;
      
      let keyUpKeyDownAttributes = "";
      if(item.enterKeyTriggersGeneration) {
        keyUpKeyDownAttributes = `onkeyup="if(event.which === 13 && !event.shiftKey) { document.querySelector('#generateButtonEl').click(); }" onkeydown="if(event.which === 13 && !event.shiftKey) { event.preventDefault(); }"`;
      }
      
      let foldToggleState = item.foldToggleState;
      
      let html = `
        <div class="input-ctn input-type-text ${item.takesUpFullRow ? "takesUpFullRow" : ""}" data-fold-toggle-state="${foldToggleState}">
          <div class="input-inner" style="${widthStyle}">
            <div class="input-label">
              <span style="width:max-content;">${item.label || item.getName.sentenceCase}</span>
              ${tipText ? ` <span class="input-tip-btn" title="${tipText}" onclick="alert(this.title)">ⓘ</span>` : ""}
              ${foldToggleState === undefined ? "" : `<button onclick="let s = this.closest('.input-ctn').dataset.foldToggleState; if(s==='hidden') { this.closest('.input-ctn').dataset.foldToggleState='shown'; } else { this.closest('.input-ctn').dataset.foldToggleState='hidden'; }" class="input-toggle-button"></button>`}
            </div>
            <div class="input-wrapper">
              <div style="width:100%; position:relative;">
                <span style="${item.autoSuggest ? "" : "display:none;"}" class="auto-suggest-btn">💭</span>
                <input style="${item.autoSuggest ? "" : "padding-right:0;"}" class="text-input" data-name="${item.getName}" type="text" oninput="window.___inputElInputEvent746291937.bind(this)(event);" ${keyUpKeyDownAttributes}>
              </div>
              ${item.random ? `<div class="random-input-btn-ctn" data-input-name="${item.getName}"><button>🎲</button></div>` : ""}
            </div>
          </div>
        </div>`;
      
      return html;
    }
    
    if(item.type === "paragraph") {
      let widthStyle = "";
      if(item.width) widthStyle += `width:${item.width};`;
      
      let textareaStyle = "";
      let height = "4rem";
      if(item.height) {
        height = `${item.height}`;
      } else {
        if(window.innerWidth < 550 || (item.random && item.randomAppend)) {
          height = "6rem";
        }
      }
      textareaStyle += `height:${height};`;
      
      let foldToggleState = item.foldToggleState;
      
      let keyUpKeyDownAttributes = "";
      if(item.enterKeyTriggersGeneration) {
        keyUpKeyDownAttributes = `onkeyup="if(event.which === 13 && !event.shiftKey) { document.querySelector('#generateButtonEl').click(); }" onkeydown="if(event.which === 13 && !event.shiftKey) { event.preventDefault(); }"`;
      }

      let modifiersHtml = "";
      try { // try/catch just while testing this
        let modifiersContainerId = "id"+Math.random().toString().replace(".", "")+Math.random().toString().replace(".", "");
        modifiersHtml = `<div id="${modifiersContainerId}" class="input-modifiers" style="display:flex;flex-wrap: wrap;">${item.modifiers ? generateModifierHtml(item.modifiers) : ""}</div>`;
        
        if(item.modifierUpdates) {
          let handlers = item.modifierUpdates.evaluateItem.split(",").map(h => h.trim()).filter(h => h.startsWith("event:")).map(h => h.replace(/^event:/, "").split(".")).map(a => ({inputName:a[0], eventName:a[1]}));
          for(let h of handlers) {
            let handlerFunction = function() {
              document.querySelector(`#${modifiersContainerId}`).innerHTML = item.modifiers ? generateModifierHtml(item.modifiers) : "";
            };
            initHandlers.push({inputName:h.inputName, eventName:h.eventName, handler:handlerFunction});
          }
        }
      } catch(e) {
        console.log(e);
      }

      let randomAppendHtml = item.randomAppend ? `<button class="random-append-btn" style="margin-top:0.5rem;">➕</button>` : "";

      let html = `
        <div class="input-ctn input-type-paragraph ${item.takesUpFullRow ? "takesUpFullRow" : ""}" data-fold-toggle-state="${foldToggleState}">
          <div class="input-inner" style="${widthStyle}">
            <div class="input-label">
              <span style="width:max-content;">${item.label || item.getName.sentenceCase}</span>
              ${tipText ? ` <span class="input-tip-btn" title="${tipText}" onclick="alert(this.title)">ⓘ</span>` : ""}
              ${foldToggleState === undefined ? "" : `<button onclick="let s = this.closest('.input-ctn').dataset.foldToggleState; if(s==='hidden') { this.closest('.input-ctn').dataset.foldToggleState='shown'; } else { this.closest('.input-ctn').dataset.foldToggleState='hidden'; }" class="input-toggle-button"></button>`}
            </div>
            <div class="input-wrapper">
              <div style="width:100%; position:relative;">
                <span style="${item.autoSuggest ? "" : "display:none;"}" class="auto-suggest-btn">💭</span>
                <textarea style="${item.autoSuggest ? "" : "padding-right:0;"} ${textareaStyle}" class="paragraph-input" data-name="${item.getName}" oninput="window.___inputElInputEvent746291937.bind(this)(event);" ${keyUpKeyDownAttributes}></textarea>
              </div>
              ${item.random ? `<div class="random-input-btn-ctn" data-input-name="${item.getName}"><button class="random-input-btn">🎲</button><button class="ai-suggest-btn" style="margin-top:0.5rem;">🧠</button>${randomAppendHtml}</div>` : ""}
            </div>
            ${modifiersHtml}
          </div>
        </div>`;
      
      return html;
    }
    
    if(item.type === "select" || item.type === "visual-select") {
      let options = [];
      
      let thereAreImports = false;
      item.compiledOptions = new Map(); // use map to maintain insertion order when converting to `entries`
      let nameToForcedPosition = {};
      let groupIndex = -1; // all items within an import have the same group index and normally-defined items are their own single-item group - this is so we can properly do sorting at the end, so that all imports are only sorted within their group.
      
      for(let key of item.options.getAllKeys) {
        groupIndex++;
        if(key.startsWith("meta:import")) {       
          thereAreImports = true;
          let tagImportWeights = {};
          if(item.options[key].tagWeights) {
            tagImportWeights = Object.fromEntries(item.options[key].tagWeights.evaluateItem.split(",").map(t => {
              let tag = t.split(":")[0].trim();
              let factor = Number(t.split(":")[1] ?? 10); // default boost is 10. note that 'boost' can be less than 1, which supresses the tag, and can even be 0, which filters out items with that tag
              if(isNaN(factor)) factor = 10;
              return [tag, factor];
            }));
          }
          let intraGroupSortingEpsilon = 0.00001; // <-- just a little hack so that the imports that have the same score stay in the order in which they were defined in the imported list
          for(let optionItem of item.options[key].from.selectAll) {
            if(item.compiledOptions.has(optionItem.getName)) continue; // we don't overwrite ones that came before (higher takes precedence over lower)

            optionItem.___groupIndex = groupIndex;
            item.compiledOptions.set(optionItem.getName, optionItem);

            if(optionItem["meta:position"]) {
              nameToForcedPosition[optionItem.getName] = optionItem["meta:position"];
            }

            if(optionItem["meta:tags"]) {
              // for each importer tag weight, we multiply it by the matching 'inherent' weight of the tag (if it exists on this imported item).
              // note that this means that if there are *any* matches, then ONLY those matches are considered.
              optionItem.___intraGroupRankingScore = 0;
              let thereWereTagWeightMatches = false;
              for(let [tag, inherentTagWeight] of Object.entries(optionItem["meta:tags"])) { // for each tag on this imported item
                if(tagImportWeights[tag] !== undefined) { // if the importer has specified a weight for this tag
                  thereWereTagWeightMatches = true;
                  optionItem.___intraGroupRankingScore += inherentTagWeight*tagImportWeights[tag]; // add to the score
                }
              }
              // if this imported style didn't have any tags that matched any of the importer tag weights, then we set its ranking score to 1 + a small amount based on the mean of its inherent tag weights:
              if(!thereWereTagWeightMatches) {
                let inherentTagWeights = Object.values(optionItem["meta:tags"]);
                // EDIT: the problem with this is that it separates 'categories' of styles which are better defined together - e.g. if all the anime styles are defined one after the other, then it's better to leave them like that
                // so for now I'm just going to set the score to 1 if there were no matching tags. Can change this in v3 if needed.
                optionItem.___intraGroupRankingScore = 1; // + (inherentTagWeights.reduce((a,v) => a+v, 0) / inherentTagWeights.length) / 10000;
              }
            } else {
              optionItem.___intraGroupRankingScore = 1;
            }
            
            optionItem.___intraGroupRankingScore += intraGroupSortingEpsilon;
            intraGroupSortingEpsilon -= intraGroupSortingEpsilon*0.00001; // so the next item will get a slightly smaller boost
          }
        } else {
          item.options[key].___groupIndex = groupIndex;
          item.options[key].___intraGroupRankingScore = 1;
          
          item.compiledOptions.set(key, item.options[key]);
          
          if(item.options[key]["meta:position"]) {
            nameToForcedPosition[key] = item.options[key]["meta:position"];
          }
        }
      }
      
      let entries = [...item.compiledOptions.entries()];
      
      if(thereAreImports) {
        // filter out items with a score below or equal to zero
        entries = entries.filter(e => e[1].___intraGroupRankingScore > 0);

        // here we do score-based ordering while strictly maintaining group index ordering.
        // note that items from a meta:import are all part of their own group, whereas items that are defined normally are their own single-item group.
        entries.sort((a, b) => a[1].___groupIndex - b[1].___groupIndex || b[1].___intraGroupRankingScore - a[1].___intraGroupRankingScore);
      }
      
      // do forced positioning:
      for(let [name, position] of Object.entries(nameToForcedPosition)) {
        let index = entries.findIndex(([key]) => key === name);
        if(index !== -1) {
          let [removed] = entries.splice(index, 1); // remove the item from its current position
          entries.splice(position, 0, removed); // insert it into the desired position
        }
      }
      item.compiledOptions = Object.fromEntries(entries);
      
      options.push(...entries.map(([name, obj]) => name).map(n => {
        let hasSubKeys = false;
        if(item.compiledOptions[n].getAllKeys && item.compiledOptions[n].getAllKeys.length > 0) hasSubKeys = true; // hasSubKeys is needed because if it does have subkeys, then we expose the whole option object/node as `keyName`, rather than just the value
        let keyName = n;
        let display = item.compiledOptions[n].name ?? n;
        let value = item.compiledOptions[n];
        return {
          value,
          display,
          keyName:n,
          hasSubKeys,
          disabled: item.compiledOptions[n]["meta:disabled"] === true,
          image: item.compiledOptions[n]["meta:image"],
          selected: item.compiledOptions[n]["meta:selected"] === true,
        }; 
      }));
      
      let selectedOpt = options.find(opt => opt.selected);
      if(!selectedOpt) options[0].selected = true;
      
      let selectElHtml = `
        <select style="${item.type === "visual-select" ? "display:none;" : ""} width:-webkit-fill-available; width:-moz-available; width:fill-available; max-width:9rem;" data-name="${item.getName}" onchange="window.___selectElChangeEvent746291937.bind(this)(event);">
          ${options.map(opt => `<option value="${opt.hasSubKeys ? `ref:optionKeyName:${opt.keyName}` : opt.value}" ${opt.disabled ? "disabled" : ""}  ${opt.selected ? "selected" : ""}>${opt.display}</option>`).join("\n")}
        </select>
      `;
      
      // the visual select is just a "facade" over the top of a normal (hidden) select - the underlying <select> is still used for all relevant logic
      if(item.type === "visual-select") {        
        let visualSelectOptions = options.map(opt => {
          let visualHtml;
          if(false && opt.image) { // TODO: work out the best approach here (e.g. grid + "slideshow" for each style?) and then enabled this
            visualHtml = `<img src="${opt.image}" style="max-height:20vh; display:block;"><div style="font-size:85%;position: sticky;bottom: 2rem;left:0;right:0;width:100%;height: 0;text-align:center;"><span style="background:#ffffff; padding:0.25rem; border:2px solid black; border-radius:0.25rem; color:black;">${opt.display}</span></div>`;
            if(!window.__preloadedVisualSelectImageUrls_957378) window.__preloadedVisualSelectImageUrls_957378 = {};
            if(!window.__preloadedVisualSelectImageUrls_957378[opt.image]) {
              let img = document.createElement("img");
              img.src = opt.image;
              img.style.cssText = `position:fixed; width:1px; height:1px; left:-10px; top:-10px;`;
              document.body.appendChild(img);
              window.__preloadedVisualSelectImageUrls_957378[opt.image] = true;
            }
            
          } else {
            visualHtml = `<div style="display:flex; justify-content:center; align-items:center; min-height:3rem;">${opt.display}</div>`;
          }
          return {
            value: opt.hasSubKeys ? `ref:optionKeyName:${opt.keyName}` : opt.value, 
            disabled: opt.disabled,
            selected: opt.selected,
            html: `<div style="border-radius:3px; overflow:hidden;">
              <div style="overflow-x:auto; overflow-y:hidden; position:relative;">
                ${visualHtml}
              </div>
            </div>
            `,
          };
        });
        selectElHtml = `
          <button class="visual-select-btn" style="width:max-content;" onclick="window.__createSelectorModal_56974639274(JSON.parse(decodeURIComponent(this.dataset.options))).then(v => { if(v !== null) { let selectEl = this.parentElement.querySelector('select'); selectEl.value=v; selectEl.onchange({}); } })" data-options="${encodeURIComponent(JSON.stringify(visualSelectOptions))}">${options.find(opt => opt.selected).display}</button>
          ${selectElHtml}
        `;
      }
      
      let html = `<div class="input-ctn input-type-select">
        <div class="input-inner">
          <div class="input-label">
            <span style="width:max-content;">${item.label || item.getName.sentenceCase}</span>
            ${tipText ? ` <span class="input-tip-btn" title="${tipText}" onclick="alert(this.title)">ⓘ</span>` : ""}
          </div>
          <div class="input-wrapper">
            ${selectElHtml}
          </div>
        </div>
      </div>`;
      
      if(item.takesUpFullRow) {
        html = `<div style="width:100%; display:flex; align-items:center; justify-content:center;">${html}</div>`;
      }
      
      return html;
    }
    
    
  }).join("\n");
  
  window.getCurrentColorScheme = function() {
    if(localStorage.forceColorScheme !== undefined) {
      return localStorage.forceColorScheme;
    } else {
      return window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches ? "dark" : "light";
    }
  }
  
  window.toggleManualDarkMode_jv83hb27igksf = function() {
    // get current mode:
    let colorScheme = window.getCurrentColorScheme();
    
    // change mode:
    let newColorScheme = (colorScheme === "dark" ? "light" : "dark");
    localStorage.forceColorScheme = newColorScheme;
    setColorScheme(newColorScheme);
    
    // if chosen mode matches current OS default, we remove manual "forced" mode:
    let systemColorScheme = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches ? "dark" : "light";
    if(systemColorScheme === newColorScheme) {
      localStorage.removeItem("forceColorScheme");
    }
    
    // regenerate comments and gallery (to propagate forceColorScheme if needed)
    if(imageGalleryCtn8569284.querySelector("iframe")) {
      imageGalleryCtn8569284.innerHTML = ""; // need to clear it first because text-to-image plugin returns "" for gallery if one already exists (it's doing a hacky thing to prevent update() from reloading the gallery)
      imageGalleryCtn8569284.innerHTML = window.generateImageGalleryHtml358402048();
    }
    
    if(commentsPluginCtn654732.querySelector("iframe")) {
      commentsPluginCtn654732.innerHTML = "";
      commentsPluginCtn654732.appendChild(window.generateCommentsPluginElement85638294())
    }
  }

  function setColorScheme(scheme) {
    if(scheme !== "dark" && scheme !== "light") throw new Error("scheme should be 'light' or 'dark'");
    document.querySelector("#darkModeBtn_875979f948").textContent = (scheme === "dark" ? "🌄" : "🌃");
    if(scheme === "dark") {
      document.documentElement.style.colorScheme = "dark";
      document.body.style.color = "#d8d4cf";
      document.body.style.backgroundColor = "#131516";
      document.documentElement.style.setProperty('--box-color', '#2a2a2a'); // CAUTION!!!! --> do not rename this variable - it is being used in "user-land" on some generators
      document.documentElement.style.setProperty('--active-comment-channel-tab-color', '#5b5b5b');
    } else {
      document.documentElement.style.colorScheme = "light";
      document.body.style.color = "black";
      document.body.style.backgroundColor = "white";
      document.documentElement.style.setProperty('--box-color', '#ebebeb');
      document.documentElement.style.setProperty('--active-comment-channel-tab-color', '#c6c6c6');
    }
  }

  ///////////////////////////////////////////
  //     do some stuff after page loads     //
  ///////////////////////////////////////////
  window.___pageLoadHandler746291937 = function() {
    
    // let pageUrlIsShareLink = window.location.hash.slice(1).startsWith("data=");
      
    // during page load, set the chosen mode based on localStorage value if it exists:
    if(localStorage.forceColorScheme !== undefined) {
      setColorScheme(localStorage.forceColorScheme);
    } else {
      // user has not manually overwritten, so we use OS default:
      let systemIsInDarkMode = !!(window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches);
      setColorScheme(systemIsInDarkMode ? "dark" : "light");
    }
    
    if(socialFeatures === "enabled") {
      commentsPluginCtn654732.appendChild(window.generateCommentsPluginElement85638294());
    }
    
    let generateClickCount = localStorage.generateClickCount ? Number(localStorage.generateClickCount) : 0;
    
    if(thisGeneratorIsUsingClickCountBasedSocialDisplay && !localStorage.theyKnowAboutClickCountBasedSocialDisplay) {
      // clickCountBasedSocialDisplayNotifierEl.style.display = "";
    }
    
    if(generateClickCount === 0) {
      bottomClickHelperEl.style.display = "";
      leftClickHelperEl.style.display = "";
      rightClickHelperEl.style.display = "";
    }
    
    if(generateClickCount > 3) {
      shareLinkOuterCtn2893827.style.display = "";
    }
    
    // Do init stuff like triggering oninput/onchange for each input right after init.
    setTimeout(async () => {
      
      if(localStorage.autoShowSocialsNextTime && document.querySelector("#showSocialsButton927514532")) {
        if(socialFeatures != "enabled") document.querySelector("#showSocialsButton927514532").click();
      }
      
      userInputsCtn8462746262.querySelectorAll('input, select, textarea').forEach(el => {
        let userInputSpec = window.___userSettings746291937.userInputs[el.dataset.name];
        userInputSpec._element = el;
      });
      
      for(let h of initHandlers) {
        let element = window.___userSettings746291937.userInputs[h.inputName]._element;
        element.addEventListener(h.eventName, h.handler);
      }
      
      // set initial values
      userInputsCtn8462746262.querySelectorAll('input, select, textarea').forEach(el => {
        let userInputSpec = window.___userSettings746291937.userInputs[el.dataset.name];
        
        let value;
        if(userInputSpec.remember) {
          value = getRememberedInput(el.dataset.name);
        } else if(el.tagName === "TEXTAREA" || (el.tagName === "INPUT" && el.type === "text")) { // <-- just to be sure
          // value = localStorage["t2i_tempRememberedInput_fisuyr9hf_"+el.dataset.name];
        }
        if(value !== undefined) {
          el.value = value;
          // <select> options can be removed by gen authors, so if the option doesn't exist, we just set it to the first option:
          if((userInputSpec.type === "select" || userInputSpec.type === "visual-select") && ![...el.querySelectorAll(`option`)].find(el => el.value === value)) { // && userInputSpec.compiledOptions[value.replace(/^ref:optionKeyName:/, "")] === undefined
            el.value = el.querySelector("option").value;
          }
        }
        
        // NOTE: triggering change/input events like this doesn't trigger addEventListener handlers - like those added via initHandlers, above, but
        // for now this is a happy accident because it's not for modifiers to not show up until you've actually typed something - reduces UI
        // complexity on first impression.
        if(el.onchange) el.onchange({_isInitCall:true});
        if(el.oninput) el.oninput({_isInitCall:true});
      });
      
      updateInputVisibilities();
      
      // set up intervals for the rotating 'example' placeholders
      userInputsCtn8462746262.querySelectorAll('input, select, textarea').forEach(el => {
        let userInputSpec = window.___userSettings746291937.userInputs[el.dataset.name];
        
        let examplesList = userInputSpec.examples;
        if(examplesList && ((el.nodeName === "INPUT" && el.type === "text") || el.nodeName === "TEXTAREA")) {
          // set rotating placeholder text on text inputs
          function nextExample() {
            if(userInputSpec.useVariables && window[el.dataset.name.toUpperCase()]) {
              window.ignorePerchanceErrors(() => {
                el.placeholder = window[el.dataset.name.toUpperCase()].evaluateItem;
              });
            } else {
              let text = selectLeaf(examplesList).evaluateItem;
              // try a few times to get a different one to the last-displayed one:
              if(text === el.placeholder) text = selectLeaf(examplesList).evaluateItem;
              if(text === el.placeholder) text = selectLeaf(examplesList).evaluateItem;
              if(text === el.placeholder) text = selectLeaf(examplesList).evaluateItem;
              
              // simple hack to save me a headache with escaping (e.g. for scratchpad placholder text)
              text = text.replace(/&lsqb;/g, "[");
              text = text.replace(/&rsqb;/g, "]");
              text = text.replace(/&lcub;/g, "{");
              text = text.replace(/&rcub;/g, "}");
              
              el.placeholder = text;
            }
          }
          nextExample();
          setInterval(nextExample, 3500);
        }
      });
      
      // set up click handlers for the 'random' buttons:
      userInputsCtn8462746262.querySelectorAll('.random-input-btn-ctn').forEach(el => {
        if(el.querySelector(".random-input-btn")) {
          el.querySelector(".random-input-btn").onclick = function() {
            let inputEl = el.closest(".input-ctn").querySelector("input, textarea");
            let userInputSpec = window.___userSettings746291937.userInputs[el.dataset.inputName];
            if(userInputSpec.useVariables && window[el.dataset.inputName.toUpperCase()]) {
              window.clearPerchanceErrors();
              inputEl.value = window[el.dataset.inputName.toUpperCase()].evaluateItem;
            } else {
              let randomList = userInputSpec.random;
              inputEl.value = selectLeaf(randomList).evaluateItem;
            }
            inputEl.oninput({});
          };
        }
        if(el.querySelector(".ai-suggest-btn")) {
          el.querySelector(".ai-suggest-btn").onclick = async function() {
            let input = prompt("Enter some ideas/keywords and the AI will generate a full description for you.\n\n(You can also specify desired writing style/length)", window.lastUsedAiSuggestPrompt || "");
            if(input && input.trim()) {
              window.lastUsedAiSuggestPrompt = input.trim();
            } else {
              return;
            }
            let suggestBtn = el.querySelector(".ai-suggest-btn");
            suggestBtn.innerHTML = `<span style="display:inline-block; animation:rotate 1.5s linear infinite;">⏳</span>`;
            suggestBtn.disabled = true;
            let originalSuggestBtnDisplay = suggestBtn.style.display;
            
            let stopBtn = document.createElement("button");
            stopBtn.innerHTML = `✋`;
            stopBtn.style.display = "none";
            stopBtn.style.marginTop = "0.5rem";
            
            let inputEl = el.closest(".input-ctn").querySelector("input, textarea");
            let chunkCount = 0;
            let fullTextSoFar = "";
            let originalInputElHeight = inputEl.offsetHeight; // so we don't make the textarea smaller than it originally was
            inputEl.value = "Loading...";
            let pendingObj = aiText({
              instruction: `Your task is to create a description of a hypothetical image which is based on the following idea/keywords/instruction: ${input.evaluateItem}\n\nCome up with a fully-formed idea based on that instruction, and write a description/caption which fully captures all visual aspects the image. Your response should be crisp/lean/efficient, descriptive, and engaging.\n\nStart your response with "Description: The image shows" and then give a one-paragraph description which captures all visual details of the idea.`,
              startWith: `Description: The image shows`,
              stopSequences: ["\n"],
              hideStartWith: true,
              onChunk: (data) => {
                if(chunkCount === 0) {
                  inputEl.value = "";
                  stopBtn.style.display = "";
                  suggestBtn.style.display = "none";
                }
                if(!data.isFromStartWith) {
                  fullTextSoFar += data.textChunk;
                  inputEl.value = fullTextSoFar.trim()[0].toUpperCase() + fullTextSoFar.trim().slice(1);
                  if(inputEl.offsetHeight < inputEl.scrollHeight+10) {
                    inputEl.style.height = "10px"; // otherwise scrollHeight can be bigger than the text. this ensures that the textarea is short enough for the textarea scrollbar to be showing - user shouldn't see it because its done synchonously
                    inputEl.style.height = Math.max(originalInputElHeight, inputEl.scrollHeight+10) + "px";
                  }
                }
                chunkCount++;
              },
            });
            suggestBtn.after(stopBtn);
            
            stopBtn.onclick = function() {
              stopBtn.disabled = true;
              pendingObj.stop();
            };
            
            let onInputInterval = setInterval(() => inputEl.oninput({}), 2000); // so that if they click 'generate' mid-text-gen, it'll still work
            try {
              await pendingObj.onFinishPromise;
            } catch(e) {
              console.error(e);
            }
            clearInterval(onInputInterval);
            inputEl.oninput({});
            
            stopBtn.remove();
            suggestBtn.style.display = originalSuggestBtnDisplay;
            suggestBtn.innerHTML = `🧠`;
            suggestBtn.disabled = false;
          };
        }
        if(el.querySelector(".random-append-btn")) {
          el.querySelector(".random-append-btn").onclick = function() {
            let inputEl = el.closest(".input-ctn").querySelector("input, textarea");
            let userInputSpec = window.___userSettings746291937.userInputs[el.dataset.inputName];
            const randomAppendList = userInputSpec.randomAppend;
            const randomAppendStr = selectLeaf(randomAppendList).evaluateItem;

            let start = inputEl.selectionStart;
            let end = inputEl.selectionEnd;
            if(start === end) start = end = inputEl.value.length; // append when nothing selected
            inputEl.setRangeText(randomAppendStr, start, end, "select"); // undo-friendly replace/append
            inputEl.focus();
            inputEl.oninput({});
          };
        }
      });
      
      // focus on first non-hidden text input (only on non-mobile devices, otherwise onscreen keyboard pops up, which might be annoying):
      if(window.innerWidth > 700 && !window.location.hash.startsWith("#edit") && !window.alreadyFocussedTextAreaOnLoad) {
        let textInput = [...userInputsCtn8462746262.querySelectorAll('textarea, input')].filter(el => el.offsetHeight !== 0)[0];
        if(textInput) {
          let distanceFromTopOfPage = textInput.getBoundingClientRect().top;
          if(distanceFromTopOfPage < 300) { // don't focus if it's way down the page - we want to keep the page scrolled to the top on load
            textInput.focus();
          }
        }
        window.alreadyFocussedTextAreaOnLoad = true; // this is a hacky way to disable this when someone is editing a t2i-framework powered generator, since that's the only case where this plugin's code will be called multiple times (and the focus will annoyingly steal away focus from the perchance code editor)
      }
      
      // wait a 'tick' and then load share link data if it exists:
      await new Promise(r => setTimeout(r, 10));
      
      let hash = window.location.hash.slice(1);
      if(hash && hash.startsWith("data=")) {
        console.log("Loading data from URL hash...");
        let result = await loadDataFromUrlHash();
        if(result.success) {
          console.log("Loaded data from URL hash.");
        } else {
          console.log("Loading share data from URL hash failed:", result);
        }
      }
      
      // If there's an "Art Style" drop-down, then flash it a few times on page load to ensure people understand that they can change the style
      try {
        if(!localStorage.generateClickCount || Number(localStorage.generateClickCount) <= 1) {
          let artStyleInnerEl = [...document.querySelectorAll(".input-ctn.input-type-select .input-inner")].filter(el => el.textContent.toLowerCase().includes("art style"))[0];
          if(artStyleInnerEl) {
            let isDarkMode = window.matchMedia('(prefers-color-scheme: dark)').matches;
            let originalBackgroundColor = artStyleInnerEl.style.backgroundColor;
            let flashCount = 0;
            let flashInterval = setInterval(() => {
              if(artStyleInnerEl.style.backgroundColor === originalBackgroundColor) {
                artStyleInnerEl.style.backgroundColor = isDarkMode ? "#364183" : "#7fb4ff";
              } else {
                artStyleInnerEl.style.backgroundColor = originalBackgroundColor;
              }
              flashCount++;
              if(flashCount > 8) {
                clearInterval(flashInterval);
                artStyleInnerEl.style.backgroundColor = originalBackgroundColor;
              }
            }, 400);
          }
        }
      } catch(e) {
        console.error(e);
      }

      
    }, 100);
  };
  
  // Hackily trigger the page load handler with an <img> onerror because it's an easy way to execute a script right after the HTML has been rendered.
  let dummyImageScriptTrigger = `<img style="display:fixed;opacity:0;pointer-events:none;top:-2000px;" src="" onerror="window.___pageLoadHandler746291937(); this.remove();">`;
  
  window.generateFeedbackCommentsHtml_8974sklfjosifo = function() {
    // old channel name: feedback-94d35yisdfiwh (annoying to subscribe to notifications for)
    let options = {channel:"t2i-feedback", hideComments:settings.showFeedback || location.hash.startsWith("#showfeedback") ? false : true, height:settings.showFeedback || location.hash.startsWith("#showfeedback") ? 500 : 220, width:300, commentPlaceholderText: "Share some feedback. Do not share personal info, this feedback data is public. If there's a technical issue/bug 𝘁𝗿𝘆 𝘂𝘀𝗶𝗻𝗴 𝗖𝗵𝗿𝗼𝗺𝗲 𝗼𝗿 𝗙𝗶𝗿𝗲𝗳𝗼𝘅, otherwise give as much detail as you can about the issue, including your device (e.g. Android/iPhone). If the issue is about bad outputs, specify the chosen style and prompt you used (if relevant).", submitButtonText: "submit feedback", hideSettingsButton:true, hideFullscreenButton:true};
    if(localStorage.forceColorScheme) options.forceColorScheme = localStorage.forceColorScheme;
    return window.___commentsPlugin746291937(options);
  }
  
  window.generateCommentsPluginElement85638294 = function() {
    let defaultChannelOptions = window.___userSettings746291937.defaultCommentOptions;
    let channels = window.___userSettings746291937.commentChannels;
    if(!channels) {
      let styleEl = document.createElement("style");
      styleEl.textContent = `#hideCommentsBtn85937402 { display:none !important; }`;
      document.head.append(styleEl);
      return document.createElement("div");
    }
    return tabbedCommentsPlugin({channels, defaultChannelOptions});
    
    // let defaultCommentOptions = window.___userSettings746291937.defaultCommentOptions;
    // let commentChannelsList = window.___userSettings746291937.commentChannels;
    // let items = [];
    // for(let channel of commentChannelsList.selectAll) {
    //   let item = {name:channel.getName, label:channel.label};
    //   let commentOptions = {};
    //   // get default options:
    //   for(let key of defaultCommentOptions.getAllKeys) {
    //     commentOptions[key] = defaultCommentOptions[key];
    //   }
    //   // apply channel-specific comment option overrides:
    //   for(let key of channel.getAllKeys) { 
    //     commentOptions[key] = channel[key];
    //   }
    //   if(channel.getName !== "general") { // <-- for backwards compatibility with v1 of this framework (i.e. to keep comment history)
    //     commentOptions.channel = channel.getName;
    //   }
    //   // Note: If you change stuff here, make sure to update the custom tab creation code in createCommentsTabs too.
    //   if(localStorage.forceColorScheme) commentOptions.forceColorScheme = localStorage.forceColorScheme;
    //   item.content = window.___commentsPlugin746291937(commentOptions);
    //   items.push(item);
    // }
    // return createCommentsTabs(items, {allowCustomChannels:!!commentChannelsList.allowCustomChannels});
  }
  
  window.generateImageGalleryHtml358402048 = function() {
    const settings = window.___userSettings746291937;
    if(!settings.galleryOptions || !settings.galleryOptions.gallery) {
      let styleEl = document.createElement("style");
      styleEl.textContent = `#hideGalleryBtn85937402 { display:none !important; }`;
      document.head.append(styleEl);
      return "";
    }
    settings.galleryOptions.forceColorScheme = null;
    if(localStorage.forceColorScheme) settings.galleryOptions.forceColorScheme = localStorage.forceColorScheme;

    // Integrate privateSave and gallery customButton features:
    if(settings.imageButtons?.privateSave) {
      const blobToDataURL = b => new Promise(r=>{const fr=new FileReader();fr.onload=()=>r(fr.result);fr.readAsDataURL(b);});
      if(!settings.galleryOptions.customButton) {
        settings.galleryOptions.customButton = {
          emoji: "⭐",
          onClick: async (data) => {
  
            let { prompt, negativePrompt, seed, guidanceScale } = data;
            let imageBlob = await fetch(data.imageUrl).then(r => r.blob());
            await window.t2i_privateGallery.add(imageBlob, {info:{prompt, negativePrompt, seed, guidanceScale}});
            
            let dataUrl = await blobToDataURL(imageBlob)
            let imgEl = document.createElement("img");
            imgEl.style.cssText = "opacity:0.5; pointer-events:none; background:#333; border-radius:3px; width:200px; height:200px; position:fixed; top:calc(50% - 50px); left:calc(50% - 50px); object-fit:contain; object-position:center;";
            imgEl.src = dataUrl;
            document.body.append(imgEl);
            animateImageIntoTarget({imgEl, sourceEl:imgEl, targetEl:privateGalleryCtn97364827.querySelector("button"), durationMs:700, wiggle:true});
          },
        };
      }
    }
    // Use the second custom button for comments popup on each image:
    if(!settings.galleryOptions.customButton2) {
      settings.galleryOptions.customButton2 = {
        emoji: "💬",
        onClick: async (data) => {
          let options = {
            channel: "image-"+data.imageId.slice(0, 25),
            style: "display:block; height:100%; width:100%;",
          };
          let defaultChannelOptions = window.___userSettings746291937.defaultCommentOptions;
          for(let key of ["commentPlaceholderText", "submitButtonText", "newestCommentsAtTop", "customEmojis", "customEmojiSize", "hideDates", "slashCommands", "forceColorScheme", "messageBubbleStyle", "messageFeedStyle", "inputAreaStyle", "submitButtonStyle", "settingsButtonStyle", "fullscreenButtonStyle", "submitButtonSuccessText", "hideSettingsButton", "hideFullscreenButton", "loadFonts", "adminPasswordHash", "adminFlair", "deleteButtonIcon", "bannedUsers", "bannedWords", "rateLimits"]) {
            if(defaultChannelOptions[key] !== undefined) options[key] = defaultChannelOptions[key];
          }
          showModal(`<div style="height:95vh; width:95vw; max-height:600px; max-width:900px;">${window.___commentsPlugin746291937(options)}</div>`);
        },
      };
    }
    
    return window.___textToImagePlugin746291937(settings.galleryOptions).evaluateItem;
  }
  
  try {
    let isSafari = navigator.vendor && navigator.vendor.indexOf('Apple') > -1 && navigator.userAgent && navigator.userAgent.indexOf('CriOS') == -1 && navigator.userAgent.indexOf('FxiOS') == -1;
    let isTouchScreen = window.matchMedia("(pointer: coarse)").matches;
    if(isSafari && window.innerWidth < 800 && isTouchScreen) {
      let viewportMetaEl = document.querySelector("[name=viewport]");
      if(!viewportMetaEl.getAttribute("content").includes("maximum-scale")) {
        viewportMetaEl.setAttribute("content", viewportMetaEl.getAttribute("content") + ", maximum-scale=1");
      }
      console.log("Safari iOS detected. Added maximum-scale attribute to prevent zooming:", viewportMetaEl.getAttribute("content"));
    }
  } catch(e) {
    console.error(e);
  }
  
  return `<style>
    body *:not(a, button) {
      color: inherit;
    }
    .input-ctn {
      width: min-content;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .input-ctn.takesUpFullRow {
      width: 100%;
    }
    .input-ctn.takesUpFullRow\\[data-fold-toggle-state='shown'\\] {
      width: 100%;
    }
    .input-ctn.takesUpFullRow\\[data-fold-toggle-state='hidden'\\] {
      width: min-content;
    }
    .input-ctn\\[data-fold-toggle-state='hidden'\\] .input-wrapper {
      display: none;
    }
    .input-inner {
      padding: 0.25rem; /* <-- if you change this, you need to change the CSS width calc on input-wrapper, generated by the JS code */
      margin: 0.25rem;
      border-radius: 3px;
      background: var(--box-color, #ebebeb);
    }
    .input-type-text .input-inner, .input-type-paragraph .input-inner {
      width: 300px;
      /* min-width: max-content; */ /* this breaks display on mobile */
    }
    .input-label {
      font-size: 80%; 
      text-align: left;
      display: flex;
      align-items: center;
    }
    .input-ctn\\[data-fold-toggle-state='shown'\\] .input-toggle-button {
      margin-left: auto;
    }
    .input-ctn\\[data-fold-toggle-state='hidden'\\] .input-toggle-button {
      margin-left: 1rem;
    }
    .input-ctn\\[data-fold-toggle-state='hidden'\\] .input-toggle-button::before {
      content: "show";
    }
    .input-ctn\\[data-fold-toggle-state='shown'\\] .input-toggle-button::before {
      content: "hide";
    }
    .input-wrapper {
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .input-wrapper .text-input {
      width: 100%;
      padding-right: 1.375rem;
      display: block;
    }
    .input-wrapper .paragraph-input {
      width: 100%;
      padding-right: 1.375rem;
      display: block;
    }
    .random-input-btn-ctn {
      margin-left: 0.25rem;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      margin-bottom: auto;
    }
    .auto-suggest-btn {
      position:absolute;
      top:0.125rem;
      right:0.125rem;
      cursor:pointer;
      opacity:0.6;
    }
    .auto-suggest-btn:hover {
      opacity:1;
    }
    .input-tip-btn {
      cursor:pointer;
      font-weight:bold;
      margin-left:0.25rem;
    }
    @keyframes rotate{
      to { transform: rotate(360deg); }
    }
    
    .tabs-header {
      display: flex;
      font-size: 70%;
      overflow:auto;
    }
    .tab {
      padding: 0.25rem;
      cursor: pointer;
      background: var(--box-color, #ebebeb);
      border-radius: 0.25rem;
      margin: 0.25rem;
      user-select: none;
      min-width: max-content;
    }
    .active-tab {
      background: var(--active-comment-channel-tab-color, #c6c6c6);
    }
    .tab-content {
      display: none;
    }
    .active-content {
      display: block;
    }
    
    .t2i-under-each-image-ctn {
      font-size: 80%;
    }
    .t2i-under-each-image-ctn .personality-chat-button { opacity: 0.5; }
    .t2i-under-each-image-ctn .personality-chat-button:hover { opacity: 1; }
    .t2i-under-each-image-ctn .private-save-button { opacity: 0.5; }
    .t2i-under-each-image-ctn .private-save-button:hover { opacity: 1; }
    iframe.text-to-image-plugin-image-iframe {
      max-width: 100%;
    }
  </style>

  <h1 id="pageTitleEl48759084793" style="font-size:1.2rem; margin-bottom:0.5rem;">${settings.pageTitle}</h1> 

  <div id="mainColumnEl85394739" style="margin:0 auto; max-width:1000px;"> <!-- NOTE: if you change 1000px to something else, ctrl+f for mainColumnEl85394739 and change the numImage-based stuff -->
    <div id="userInputsCtn8462746262" style="display:flex; padding:0.25rem; justify-content:center; flex-wrap:wrap;">
      ${userInputsHtml}
    </div>
    ${dummyImageScriptTrigger}

    <!--<div style="margin:1rem 0; display: flex; align-items: center; justify-content: center;">
      ➡️&nbsp;<button id="generateButtonEl" style="font-size:1.5rem;" onclick="window.___generateButtonClickEvent746291937(event);">✨ generate</button>&nbsp;⬅️
    </div>-->
    
    <div id="shareLinkOuterCtn2893827" style="display:none; margin-bottom:2rem;">
      <button id="shareSettingsBtn758374" onclick="window.__generateSettingsShareLink084722()" style="font-size:70%;">🔗 share these settings</button>
      <div id="shareLinkCtn83927376" style="display:none; margin-top:0.5rem; font-size:80%;">
        <input style="width:200px;" id="shareSettingsLinkInputEl759307" disabled> <button style="min-width:80px;" onclick="navigator.clipboard.writeText(shareSettingsLinkInputEl759307.value).then(r => this.innerHTML='✅'); setTimeout(() => this.innerHTML='copy link', 2000);">copy link</button>
        <div style="font-size:80%; opacity:0.7;">(this link contains a snapshot of the above settings)</div>
      </div>
    </div>
    
    <div style="margin:1rem 0;">
      <div style="display: flex;align-items: center;justify-content: center;"><span id="leftClickHelperEl" style="display:none;">click ➡&nbsp;</span><button id="generateButtonEl" style="font-size:1.5rem;" onclick="window.___generateButtonClickEvent746291937(event);">✨ generate</button><span id="rightClickHelperEl" style="display:none;">&nbsp;⬅ click</span></div>
      <div id="bottomClickHelperEl" style="display:none;"><div>⬆</div><div>click</div></div>
    </div>

    <div id="outputAreaEl" style="margin-top:1rem; min-height:12rem; display:flex; flex-wrap:wrap; justify-content:center; align-items:center; padding:0.25rem;">
      <span>${settings.introMessage || "↑ Set inputs and click <i>generate</i> to begin. ↑"}</span>
    </div>
    
    <div style="position:fixed; bottom:0.5rem; right:0.5rem; text-align:right; z-index:100;">
      <div id="feedbackCommentsCtn"></div>
      <button id="feedbackBtn893745ykfuhd" onclick="if(feedbackCommentsCtn.innerHTML.length === 0) { feedbackCommentsCtn.innerHTML=window.generateFeedbackCommentsHtml_8974sklfjosifo(); this.innerHTML='✖ close'; } else {  feedbackCommentsCtn.innerHTML='';  this.innerHTML='🗨️ feedback'; }" style="font-size:0.85rem;">🗨️ feedback</button>
    </div>
    
    <div id="showSocialsButtonCtn9000274628" style="${socialFeatures === 'button' ? '' : 'display:none;'}">
      <button id="showSocialsButton927514532" style="margin-top:2rem; margin-bottom:0.5rem;" onclick="window.__showSocialsButtonClickHandler98347938();"><div style="padding:0.5rem;">💬 show comments & gallery 🖼️</div></button>
      <div style="display:flex; align-items:center; justify-content:center;">
        <input id="autoShowCheckbox84937483" style="cursor:pointer;" type="checkbox"><span style="margin-left:0.25rem; cursor:pointer;" onclick="autoShowCheckbox84937483.checked=!autoShowCheckbox84937483.checked;">auto-show next time</span>
      </div>
    </div>

    <div id="showHideGalleryAndCommentsButtonsCtn83728732671" style="${socialFeatures === "button" || socialFeatures === "disabled" ? "display:none;" : ""} margin-top:1rem;">
      <button id="hideCommentsBtn85937402" onclick="if(this.dataset.hidden) { this.dataset.hidden=''; this.textContent = 'hide comments'; commentsPluginCtn654732.style.display = ''; } else { this.dataset.hidden='1'; this.textContent = 'show comments'; commentsPluginCtn654732.style.display = 'none'; }" data-hidden="">hide comments</button>
      <button id="hideGalleryBtn85937402" onclick="if(this.dataset.hidden) { this.dataset.hidden=''; this.textContent = 'hide gallery'; imageGalleryCtn8569284.style.display = ''; } else { this.dataset.hidden='1'; this.textContent = 'show gallery'; imageGalleryCtn8569284.style.display = 'none'; }" data-hidden="">hide gallery</button>
    </div>
    
    <div style="position:fixed; bottom:0.5rem; left:0.5rem;">
      <button id="darkModeBtn_875979f948" style="cursor:pointer; font-size:0.85rem;" onclick="window.toggleManualDarkMode_jv83hb27igksf();">🌃</button>
      <div style="display:inline-block; font-size:0.85rem;">[window.___fullscreenButton893479("&nbsp;&nbsp;&nbsp;⇱&nbsp;&nbsp;&nbsp;", "&nbsp;&nbsp;&nbsp;⇲&nbsp;&nbsp;&nbsp;", "line-height:initial;")]</div>
    </div>
  </div>
  
  <div id="privateGalleryCtn97364827" ${settings.imageButtons?.privateSave ? "" : "hidden"} style="pointer-events:none;bottom:0.5rem;left:0;right:0;width:100%;text-align:center;position:fixed;z-index:100;font-size:0.85rem;">
    <button onclick="window.t2i_privateGallery.show()" title="Ctrl+Shift+G" style="pointer-events:auto; cursor:pointer;">🛡️ ${window.innerWidth > 600 ? "show " : ""}private gallery</button>
  </div>
  
  <div id="commentsPluginCtn654732" style="padding:0.25rem; width:100%; margin:0 auto; margin-top:1rem;"></div>
  
  <!-- <div style="background-image:url(https://example.com/image.webp); filter: blur(1px); opacity:0.5; background-position:center; background-size:cover; z-index:-10; position:fixed; top:0;left:0;right:0;bottom:0;"></div> -->
  <div id="clickCountBasedSocialDisplayNotifierEl" style="font-size:80%; max-width:800px; margin:0 auto; padding:0.5rem; text-align:left; display:none;">
    <div style="background:var(--box-color);padding: 0.5rem; border-radius:3px;"><b>Note:</b> The comments and gallery only appear after generating several batches of images. If you visit this page again later and don't see the gallery/comments, it's because your cookies/storage for this site got cleared for some reason, so you'd need to generate some more images. <button style="font-size:80%;" onclick="localStorage.theyKnowAboutClickCountBasedSocialDisplay='1'; this.parentElement.style.display='none';">✅ got it</button></div>
  </div>
  
  <div id="imageGalleryCtn8569284" style="margin-top:1rem;">
    ${["disabled", "button"].includes(socialFeatures) ? "" : window.generateImageGalleryHtml358402048()}
  </div>
  <br><br><br><br>
  `.replaceAll("{", "\\{").replaceAll("}", "\\}");
  
  
  
createCommentsTabs(items, opts) => // items = [{name, label, content}, {name, label, content}, ...]
  if(!opts) opts = {};
  let ctn = document.createElement("div");
  ctn.innerHTML = `
    <div class="tabs-header"></div>
    <div class="tabs-content"></div>`;

  const tabsHeaderEl = ctn.querySelector('.tabs-header');
  const tabsContentEl = ctn.querySelector('.tabs-content');

  // Function to handle tab click
  function handleTabClick(event) {
    let clickedTab = event.target;
    if(!clickedTab.classList.contains('tab')) return;
    
    if(event.target.classList.contains('addCustomChannel')) {
      let name = prompt("Choose a channel name. Others will only see this channel if they also add it. Start the name with an exclaimation mark to remove an existing channel.");
      if(name) {
        let removeChannel = false;
        if(name.startsWith("!")) {
          removeChannel = true;
          name = name.slice(1);
        }
        name = name.toLowerCase();
        name = name.replace(/[^a-z0-9\-]/g, "");
        
        if(!name) {
          alert("Channel names can only contain lower-case letters, numbers and hyphens.");
          return;
        }
        if(name === "t2i-feedback") return;
        
        if(!localStorage.__t2iFrameworkCustomChannels) localStorage.__t2iFrameworkCustomChannels = "{}";
        let customChannels = {};
        try { customChannels = JSON.parse(localStorage.__t2iFrameworkCustomChannels) } catch(e) { console.error("Failed to parse custom channels from localStorage:", e); }
        if(removeChannel) {
          delete customChannels[name];
          tabsHeaderEl.querySelector(`[data-name='${name}']`).remove();
          tabsContentEl.querySelector(`[data-name='${name}']`).remove();
          showTabByName(tabsHeaderEl.firstElementChild.dataset.name);
        } else {
          if(!customChannels[name]) {
            customChannels[name] = {name:name, label:name};
            let item = JSON.parse(JSON.stringify(customChannels[name]));
            item.content = createCustomTabContent(item);
            addTab(item);
            tabsHeaderEl.append(event.target); // move "+" button to the end
          }
          showTabByName(name);
        }
        localStorage.__t2iFrameworkCustomChannels = JSON.stringify(customChannels);
      }
      return;
    } else {
      showTabByName(clickedTab.dataset.name);
    }
  }
  
  function showTabByName(name) {
    tabsHeaderEl.querySelectorAll('.tab').forEach(el => el.classList.remove('active-tab'));
    tabsHeaderEl.querySelector(`.tab[data-name='${name}']`).classList.add('active-tab');
    
    tabsContentEl.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active-content'));
    tabsContentEl.querySelector(`.tab-content[data-name='${name}']`).classList.add('active-content');
  }
  
  function addTab(item) {
    const tab = document.createElement('div');
    tab.className = 'tab';
    tab.textContent = item.label;
    tab.dataset.name = item.name;

    const tabContent = document.createElement('div');
    tabContent.className = 'tab-content';
    tabContent.innerHTML = item.content;
    tabContent.dataset.name = item.name;
    
    tabsHeaderEl.appendChild(tab);
    tabsContentEl.appendChild(tabContent);
    return {tab, tabContent};
  }
  
  function createCustomTabContent(item) {
    let commentOptions = JSON.parse(JSON.stringify(item));
    let defaultCommentOptions = window.___userSettings746291937.defaultCommentOptions;
    for(let key of defaultCommentOptions.getAllKeys) {
      commentOptions[key] = defaultCommentOptions[key];
    }
    if(localStorage.forceColorScheme) {
      commentOptions.forceColorScheme = localStorage.forceColorScheme;
    }
    commentOptions.channel = commentOptions.name;
    delete commentOptions.name;
    return window.___commentsPlugin746291937(commentOptions);
  }

  let i = 0;
  for(let item of items) {
    let {tab, tabContent} = addTab(item);
    if(i === 0) {
      tabContent.classList.add('active-content');
      tab.classList.add('active-tab');
    }
    i++;
  }
  
  if(opts.allowCustomChannels) {
    // add user-specified custom channels:
    if(!localStorage.__t2iFrameworkCustomChannels) localStorage.__t2iFrameworkCustomChannels = "{}";
    let customChannels = {};
    try { customChannels = JSON.parse(localStorage.__t2iFrameworkCustomChannels) } catch(e) { console.error("Failed to parse custom channels from localStorage:", e); }
    for(let item of Object.values(customChannels)) {
      let itemCopy = JSON.parse(JSON.stringify(item));
      itemCopy.content = createCustomTabContent(itemCopy);
      addTab(itemCopy);
    }
    // add the "+" button:
    const tab = document.createElement('div');
    tab.className = 'tab addCustomChannel';
    tab.textContent = "+";
    tab.style.cssText = "min-width:1.4rem;";
    tabsHeaderEl.appendChild(tab);
  }
  
  tabsHeaderEl.addEventListener('click', handleTabClick);

  return ctn;




async showModal(html) =>
  if(!html) html = "<p>?</p>";
  return new Promise(resolve => {
    const overlay = Object.assign(document.createElement("div"), { tabIndex: 0 });
    overlay.style.cssText = `position:fixed;inset:0;z-index:9999;display:grid;place-items:center;background-color:rgba(0,0,0,.65);font:16px/1.4 system-ui`;
    overlay.innerHTML = `<div style="position:relative;">
      <button style="position:absolute;z-index:10;top:-12px;right:-12px;width:28px;height:28px;border:none;border-radius:50%;background-color:light-dark(#fff,#444);color:light-dark(#666,#aaa);cursor:pointer;font-size:18px;line-height:1;padding:0;box-shadow:0 2px 4px rgba(0,0,0,.2);">✕</button>
      <div style="text-align:left !important;border-radius:8px;background-color:light-dark(#fff,#222);color:light-dark(#000,#fff);box-shadow:0 2px 8px rgba(0,0,0,.2);">
        ${html}
      </div>
    </div>`;
    const [wrapper, closeBtn] = [overlay.firstChild, overlay.querySelector("button")];
    const finish = () => { overlay.remove(); resolve(); };
    closeBtn.onclick = finish;
    overlay.onmousedown = e => { if(e.target === overlay) finish(); };
    overlay.onkeydown = e => { if(e.key === "Escape") finish(); };
    document.body.append(overlay);
  });

async confirmAsync(message, opts) =>
  if(!opts) opts = {};
  if(!message) message = "Are you sure?"
  return new Promise(resolve => {
    const overlay = Object.assign(document.createElement("div"), { tabIndex: 0 });
    overlay.style.cssText = `position:fixed;inset:0;z-index:9999;display:grid;place-items:center;background-color:rgba(0,0,0,.65);font:16px/1.4 system-ui`;
    overlay.innerHTML = `<div style="text-align:left !important;max-width:min(97vw, 450px);padding:15px;border-radius:8px;background-color:light-dark(#fff,#222);color:light-dark(#000,#fff);box-shadow:0 2px 8px rgba(0,0,0,.2);">
      <p style="margin:0 0 20px;white-space:pre-wrap;">${message.replace(/[<>&]/g, m => ({"<":"&lt;","&":"&amp;",">":"&gt;"}[m]))}</p>
      <div style="display:flex;justify-content:flex-end;gap:8px;">
        <button ${opts.hideCancel ? "hidden" : ""} style="padding:6px 16px;border:1px solid light-dark(#ccc,#555);border-radius:6px;background-color:light-dark(#f6f6f6,#333);color:inherit;cursor:pointer;">Cancel</button>
        <button autofocus style="padding:6px 16px;border:none;border-radius:6px;background-color:light-dark(#1677ff,#2b87ff);color:#fff;cursor:pointer;">Okay</button>
      </div>
    </div>`;
    const [cancelBtn, okBtn] = overlay.querySelectorAll("button");
    const finish = val => { overlay.remove(); resolve(val); };
    cancelBtn.onclick = () => finish(false);
    okBtn.onclick = () => finish(true);
    overlay.onkeydown = e => {
      if (e.key === "Escape") finish(false);
      else if (e.key === "Enter") finish(true);
    };
    document.body.append(overlay);
    overlay.focus({ preventScroll: true }); // enables Esc handling immediately
  });
