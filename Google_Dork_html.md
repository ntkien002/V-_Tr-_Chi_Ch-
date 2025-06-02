<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Google Dorks for Bug Bounty</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            color: #fff;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .github-link {
            color: #4ecdc4;
            text-decoration: none;
            font-size: 1.1rem;
            transition: color 0.3s ease;
        }

        .github-link:hover {
            color: #ff6b6b;
        }

        .input-section {
            background: rgba(255, 255, 255, 0.15);
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 30px;
            text-align: center;
        }

        .input-section label {
            display: block;
            font-size: 1.2rem;
            margin-bottom: 15px;
            font-weight: 600;
        }

        .domain-input {
            width: 100%;
            max-width: 400px;
            padding: 15px 20px;
            font-size: 1.1rem;
            border: none;
            border-radius: 25px;
            background: rgba(255, 255, 255, 0.9);
            color: #333;
            text-align: center;
            outline: none;
            transition: all 0.3s ease;
        }

        .domain-input:focus {
            transform: scale(1.05);
            box-shadow: 0 0 20px rgba(78, 205, 196, 0.5);
        }

        .dorks-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
            gap: 20px;
        }

        .dork-category {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 20px;
            transition: all 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .dork-category:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2);
            background: rgba(255, 255, 255, 0.15);
        }

        .category-title {
            font-size: 1.3rem;
            font-weight: bold;
            margin-bottom: 15px;
            color: #4ecdc4;
            border-bottom: 2px solid #4ecdc4;
            padding-bottom: 8px;
        }

        .dork-item {
            background: rgba(0, 0, 0, 0.3);
            padding: 12px 15px;
            margin: 8px 0;
            border-radius: 8px;
            font-family: 'Courier New', monospace;
            font-size: 0.9rem;
            line-height: 1.4;
            cursor: pointer;
            transition: all 0.2s ease;
            position: relative;
            overflow: hidden;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-left: 4px solid transparent;
        }

        .dork-item.checked {
            background: rgba(78, 205, 196, 0.2);
            border-left: 4px solid #4ecdc4;
        }

        .dork-item.checked .dork-number {
            background: #4ecdc4;
            color: #fff;
        }

        .dork-item:hover {
            background: rgba(0, 0, 0, 0.5);
            transform: scale(1.02);
        }

        .dork-item:active {
            transform: scale(0.98);
        }

        .dork-text {
            flex: 1;
            margin-right: 10px;
            margin-left: 10px;
        }

        .dork-number {
            background: rgba(255, 255, 255, 0.2);
            color: #fff;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 0.8rem;
            flex-shrink: 0;
            transition: all 0.3s ease;
        }

        .dork-actions {
            display: flex;
            gap: 8px;
        }

        .action-btn {
            background: rgba(78, 205, 196, 0.2);
            border: 1px solid rgba(78, 205, 196, 0.4);
            color: #4ecdc4;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.8rem;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .action-btn:hover {
            background: rgba(78, 205, 196, 0.4);
            color: #fff;
        }

        .search-btn {
            background: rgba(255, 107, 107, 0.2);
            border: 1px solid rgba(255, 107, 107, 0.4);
            color: #ff6b6b;
        }

        .search-btn:hover {
            background: rgba(255, 107, 107, 0.4);
            color: #fff;
        }

        .copy-tooltip {
            position: absolute;
            top: -30px;
            right: 10px;
            background: #333;
            color: #fff;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 0.8rem;
            opacity: 0;
            transition: opacity 0.3s ease;
            pointer-events: none;
            white-space: nowrap;
        }

        .dork-item.copied .copy-tooltip {
            opacity: 1;
        }

        .placeholder-text {
            color: #ff6b6b;
            font-weight: bold;
        }

        @media (max-width: 768px) {
            .container {
                padding: 20px;
                margin: 10px;
            }
            
            .header h1 {
                font-size: 2rem;
            }
            
            .dorks-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Google Dorks for Bug Bounty</h1>
            <a href="#" class="github-link">GitHub Repo</a>
        </div>

        <div class="input-section">
            <label for="domainInput">Enter a domain:</label>
            <input type="text" id="domainInput" class="domain-input" placeholder="example.com" />
        </div>

        <div class="dorks-grid" id="dorksGrid">
            <!-- Dorks will be populated here -->
        </div>
    </div>

    <script>
        const dorkCategories = [
            {
                title: "PHP extension w/ parameters",
                dorks: ["site:example.com ext:php inurl:?"]
            },
            {
                title: "API Endpoints",
                dorks: ["site:example[.]com inurl:api | site:*/rest | site:*/v1 | site:*/v2 | site:*/v3"]
            },
            {
                title: "Juicy Extensions",
                dorks: ["site:\"example[.]com\" ext:log | ext:txt | ext:conf | ext:cnf | ext:ini | ext:env | ext:sh | ext:bak | ext:backup | ext:swp | ext:old | ext:~ | ext:git | ext:svn | ext:htpasswd | ext:htaccess | ext:json"]
            },
            {
                title: "High % inurl keywords",
                dorks: ["inurl:conf | inurl:env | inurl:cgi | inurl:bin | inurl:etc | inurl:root | inurl:sql | inurl:backup | inurl:admin | inurl:php site:example[.]com"]
            },
            {
                title: "Server Errors",
                dorks: ["inurl:\"error\" | intitle:\"exception\" | intitle:\"failure\" | intitle:\"server at\" | inurl:exception | \"database error\" | \"SQL syntax\" | \"undefined index\" | \"unhandled exception\" | \"stack trace\" site:example[.]com"]
            },
            {
                title: "XSS prone parameters",
                dorks: ["inurl:q= | inurl:s= | inurl:search= | inurl:query= | inurl:keyword= | inurl:lang= inurl:& site:example.com"]
            },
            {
                title: "Open Redirect prone parameters",
                dorks: ["inurl:url= | inurl:return= | inurl:next= | inurl:redirect= | inurl:redir= | inurl:ret= | inurl:r2= | inurl:page= inurl:& inurl:http site:example.com"]
            },
            {
                title: "SQLi Prone Parameters",
                dorks: ["inurl:id= | inurl:pid= | inurl:category= | inurl:cat= | inurl:action= | inurl:sid= | inurl:dir= inurl:& site:example.com"]
            },
            {
                title: "SSRF Prone Parameters",
                dorks: ["inurl:http | inurl:url= | inurl:path= | inurl:dest= | inurl:html= | inurl:data= | inurl:domain= | inurl:page= inurl:& site:example.com"]
            },
            {
                title: "LFI Prone Parameters",
                dorks: ["inurl:include | inurl:dir | inurl:detail= | inurl:file= | inurl:folder= | inurl:inc= | inurl:locate= | inurl:doc= | inurl:conf= inurl:& site:example.com"]
            },
            {
                title: "RCE Prone Parameters",
                dorks: ["inurl:cmd | inurl:exec= | inurl:query= | inurl:code= | inurl:do= | inurl:run= | inurl:read= | inurl:ping= inurl:& site:example.com"]
            },
            {
                title: "File upload endpoints",
                dorks: ["site:example.com \"choose file\""]
            },
            {
                title: "API Docs",
                dorks: ["inurl:apidocs | inurl:api-docs | inurl:swagger | inurl:api-explorer site:\"example[.]com\""]
            },
            {
                title: "Login Pages",
                dorks: ["inurl:login | inurl:signin | intitle:login | intitle:signin | inurl:secure site:example[.]com"]
            },
            {
                title: "Test Environments",
                dorks: ["inurl:test | inurl:env | inurl:dev | inurl:staging | inurl:sandbox | inurl:debug | inurl:temp | inurl:internal | inurl:demo site:example.com"]
            },
            {
                title: "Sensitive Documents",
                dorks: ["site:example.com ext:txt | ext:pdf | ext:xml | ext:xls | ext:xlsx | ext:ppt | ext:pptx | ext:doc | ext:docx intext:\"confidential\" | intext:\"Not for Public Release\" | intext:\"internal use only\" | intext:\"do not distribute\""]
            },
            {
                title: "Sensitive Parameters",
                dorks: ["inurl:email= | inurl:phone= | inurl:password= | inurl:secret= inurl:& site:example[.]com"]
            },
            {
                title: "Adobe Experience Manager (AEM)",
                dorks: ["inurl:/content/usergenerated | inurl:/content/dam | inurl:/jcr:content | inurl:/libs/granite | inurl:/etc/clientlibs | inurl:/content/geometrixx | inurl:/bin/wcm | inurl:/crx/de site:example[.]com"]
            },
            {
                title: "Disclosed XSS and Open Redirects",
                dorks: ["site:openbugbounty.org inurl:reports intext:\"example.com\""]
            },
            {
                title: "Google Groups",
                dorks: ["site:groups.google.com \"example.com\""]
            },
            {
                title: "Code Leaks",
                dorks: [
                    "site:pastebin.com \"example.com\"",
                    "site:jsfiddle.net \"example.com\"",
                    "site:codebeautify.org \"example.com\"",
                    "site:codepen.io \"example.com\""
                ]
            },
            {
                title: "Cloud Storage",
                dorks: [
                    "site:s3.amazonaws.com \"example.com\"",
                    "site:blob.core.windows.net \"example.com\"",
                    "site:googleapis.com \"example.com\"",
                    "site:drive.google.com \"example.com\"",
                    "site:dev.azure.com \"example[.]com\"",
                    "site:onedrive.live.com \"example[.]com\"",
                    "site:digitaloceanspaces.com \"example[.]com\"",
                    "site:sharepoint.com \"example[.]com\"",
                    "site:s3-external-1.amazonaws.com \"example[.]com\"",
                    "site:s3.dualstack.us-east-1.amazonaws.com \"example[.]com\"",
                    "site:dropbox.com/s \"example[.]com\"",
                    "site:box.com/s \"example[.]com\"",
                    "site:docs.google.com inurl:\"/d/\" \"example[.]com\""
                ]
            },
            {
                title: "JFrog Artifactory",
                dorks: ["site:jfrog.io \"example[.]com\""]
            },
            {
                title: "Firebase",
                dorks: ["site:firebaseio.com \"example[.]com\""]
            },
            {
                title: "Security.txt",
                dorks: ["site:*/security.txt \"bounty\""]
            }
        ];

        function replaceDomain(dork, domain) {
            if (!domain) return dork;
            return dork
                .replace(/example\.com/g, domain)
                .replace(/example\[?\.\]?com/g, domain);
        }

        function highlightDomain(dork, domain) {
            if (!domain) {
                return dork.replace(/(example(?:\[?\.\]?)com)/g, '<span class="placeholder-text">$1</span>');
            }
            return dork.replace(new RegExp(domain.replace(/[.*+?^${}()|[\]\\]/g, '\\$&'), 'g'), 
                `<span class="placeholder-text">${domain}</span>`);
        }

        function copyToClipboard(text) {
            navigator.clipboard.writeText(text).then(() => {
                // Handle success
            }).catch(() => {
                // Fallback for older browsers
                const textArea = document.createElement('textarea');
                textArea.value = text;
                document.body.appendChild(textArea);
                textArea.select();
                document.execCommand('copy');
                document.body.removeChild(textArea);
            });
        }

        function searchGoogle(query) {
            const encodedQuery = encodeURIComponent(query);
            const googleUrl = `https://www.google.com/search?q=${encodedQuery}`;
            window.open(googleUrl, '_blank');
        }

        function renderDorks() {
            const domain = document.getElementById('domainInput').value.trim();
            const dorksGrid = document.getElementById('dorksGrid');
            
            dorksGrid.innerHTML = '';
            let globalCounter = 1;
            
            dorkCategories.forEach(category => {
                const categoryDiv = document.createElement('div');
                categoryDiv.className = 'dork-category';
                
                const titleDiv = document.createElement('div');
                titleDiv.className = 'category-title';
                titleDiv.textContent = category.title;
                categoryDiv.appendChild(titleDiv);
                
                category.dorks.forEach(dork => {
                    const dorkDiv = document.createElement('div');
                    dorkDiv.className = 'dork-item';
                    dorkDiv.dataset.number = globalCounter;
                    
                    const replacedDork = replaceDomain(dork, domain);
                    
                    const numberDiv = document.createElement('div');
                    numberDiv.className = 'dork-number';
                    numberDiv.textContent = globalCounter;
                    
                    const dorkText = document.createElement('div');
                    dorkText.className = 'dork-text';
                    dorkText.innerHTML = highlightDomain(replacedDork, domain);
                    
                    const actionsDiv = document.createElement('div');
                    actionsDiv.className = 'dork-actions';
                    
                    const searchBtn = document.createElement('button');
                    searchBtn.className = 'action-btn search-btn';
                    searchBtn.textContent = 'Search';
                    searchBtn.onclick = (e) => {
                        e.stopPropagation();
                        searchGoogle(replacedDork);
                        markAsChecked(dorkDiv);
                    };
                    
                    const copyBtn = document.createElement('button');
                    copyBtn.className = 'action-btn';
                    copyBtn.textContent = 'Copy';
                    copyBtn.onclick = (e) => {
                        e.stopPropagation();
                        copyToClipboard(replacedDork);
                        dorkDiv.classList.add('copied');
                        setTimeout(() => {
                            dorkDiv.classList.remove('copied');
                        }, 1000);
                    };
                    
                    actionsDiv.appendChild(searchBtn);
                    actionsDiv.appendChild(copyBtn);
                    
                    const tooltip = document.createElement('div');
                    tooltip.className = 'copy-tooltip';
                    tooltip.textContent = 'Copied to clipboard!';
                    
                    dorkDiv.appendChild(numberDiv);
                    dorkDiv.appendChild(dorkText);
                    dorkDiv.appendChild(actionsDiv);
                    dorkDiv.appendChild(tooltip);
                    
                    // Click on dork text to search
                    dorkText.addEventListener('click', () => {
                        searchGoogle(replacedDork);
                        markAsChecked(dorkDiv);
                    });
                    
                    // Click on number to toggle checked state
                    numberDiv.addEventListener('click', (e) => {
                        e.stopPropagation();
                        toggleChecked(dorkDiv);
                    });
                    
                    categoryDiv.appendChild(dorkDiv);
                    globalCounter++;
                });
                
                dorksGrid.appendChild(categoryDiv);
            });
        }

        function markAsChecked(dorkDiv) {
            dorkDiv.classList.add('checked');
            saveCheckedState();
        }

        function toggleChecked(dorkDiv) {
            dorkDiv.classList.toggle('checked');
            saveCheckedState();
        }

        function saveCheckedState() {
            const checkedItems = [];
            document.querySelectorAll('.dork-item.checked').forEach(item => {
                checkedItems.push(item.dataset.number);
            });
            // Save to memory only (no localStorage)
            window.checkedDorks = checkedItems;
        }

        function restoreCheckedState() {
            if (window.checkedDorks) {
                window.checkedDorks.forEach(number => {
                    const item = document.querySelector(`[data-number="${number}"]`);
                    if (item) {
                        item.classList.add('checked');
                    }
                });
            }
        }

        document.getElementById('domainInput').addEventListener('input', () => {
            renderDorks();
            // Restore checked state after re-render
            setTimeout(restoreCheckedState, 50);
        });
        
        // Initial render
        renderDorks();
    </script>
</body>
</html>
