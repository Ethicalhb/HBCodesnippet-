<!DOCTYPE html>
<html>
<head>
    <title>HB Code Snippets Manager</title>
    <style>
        :root {
            --hb-blue: #2A5C8D;
            --hb-orange: #FF6B35;
            --hb-light: #F5F5F5;
        }

        body {
            font-family: 'Segoe UI', Arial, sans-serif;
            max-width: 1000px;
            margin: 0 auto;
            padding: 20px;
            background-color: var(--hb-light);
        }

        h1 {
            color: var(--hb-blue);
            border-bottom: 3px solid var(--hb-orange);
            padding-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .logo {
            background: var(--hb-blue);
            color: white;
            padding: 8px 12px;
            border-radius: 5px;
            font-weight: bold;
        }

        .snippet-card {
            background: white;
            padding: 20px;
            margin: 15px 0;
            border-radius: 8px;
            border-left: 4px solid var(--hb-blue);
            box-shadow: 0 3px 6px rgba(42, 92, 141, 0.1);
        }

        pre {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 6px;
            border: 1px solid #e0e0e0;
            overflow-x: auto;
        }

        form {
            background: white;
            padding: 25px;
            border-radius: 8px;
            margin-bottom: 30px;
            border: 1px solid var(--hb-blue);
        }

        input, textarea, select {
            width: 100%;
            padding: 10px;
            margin: 8px 0;
            border: 2px solid #e0e0e0;
            border-radius: 4px;
            box-sizing: border-box;
            transition: border-color 0.3s ease;
        }

        input:focus, textarea:focus, select:focus {
            border-color: var(--hb-blue);
            outline: none;
        }

        button {
            background: var(--hb-orange);
            color: white;
            padding: 12px 25px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 8px;
            font-weight: bold;
            transition: all 0.3s ease;
        }

        button:hover {
            background: #E56024;
            transform: translateY(-1px);
        }

        .danger {
            background: #dc3545;
        }

        .tag {
            background: var(--hb-blue);
            color: white;
            padding: 4px 8px;
            border-radius: 3px;
            font-size: 0.9em;
        }
    </style>
</head>
<body>
    <h1>
        <span class="logo">HB</span>
        Code Snippets Manager
    </h1>
    
    <!-- Add Snippet Form -->
    <form id="snippetForm">
        <h2 style="color: var(--hb-blue); margin-top: 0;">➕ Add New Snippet</h2>
        <input type="text" id="title" placeholder="Snippet Title" required>
        <select id="language" required>
            <option value="">Select Language</option>
            <option>JavaScript</option>
            <option>Python</option>
            <option>Java</option>
            <option>HTML</option>
            <option>CSS</option>
        </select>
        <textarea id="code" placeholder="Paste your code here..." rows="6" required></textarea>
        <input type="text" id="tags" placeholder="Tags (comma separated)">
        <button type="submit">💾 Save Snippet</button>
    </form>

    <!-- Search & Filter -->
    <div style="margin: 25px 0; display: flex; gap: 15px;">
        <input type="text" id="search" placeholder="🔍 Search snippets..." 
               style="flex: 1; padding: 10px; border: 2px solid var(--hb-blue);">
        <select id="filterLanguage" style="padding: 10px; border: 2px solid var(--hb-blue);">
            <option value="">All Languages</option>
            <option>JavaScript</option>
            <option>Python</option>
            <option>Java</option>
            <option>HTML</option>
            <option>CSS</option>
        </select>
    </div>

    <!-- Snippets Container -->
    <div id="snippetsContainer">
        <!-- Demo snippets will appear here automatically -->
    </div>

    <script>
        // [Keep the exact same JavaScript code from previous demo]
        // Only changed styling, functionality remains identical
        let snippets = JSON.parse(localStorage.getItem('snippets')) || [
            {
                id: 1,
                title: "Array Operations",
                language: "JavaScript",
                code: "const numbers = [1, 2, 3];\nconst doubled = numbers.map(n => n * 2);",
                tags: ["arrays", "functions"],
                date: new Date().toLocaleDateString()
            },
            {
                id: 2,
                title: "String Formatting",
                language: "Python",
                code: "def greet(name):\n    return f'Hello, {name}!'",
                tags: ["strings", "formatting"],
                date: new Date().toLocaleDateString()
            }
        ];

        function saveSnippets() {
            localStorage.setItem('snippets', JSON.stringify(snippets));
        }

        document.getElementById('snippetForm').addEventListener('submit', (e) => {
            e.preventDefault();
            
            const newSnippet = {
                id: Date.now(),
                title: document.getElementById('title').value,
                language: document.getElementById('language').value,
                code: document.getElementById('code').value,
                tags: document.getElementById('tags').value.split(',').map(tag => tag.trim()),
                date: new Date().toLocaleDateString()
            };

            snippets.push(newSnippet);
            saveSnippets();
            renderSnippets();
            e.target.reset();
        });

        document.getElementById('search').addEventListener('input', renderSnippets);
        document.getElementById('filterLanguage').addEventListener('change', renderSnippets);

        function renderSnippets() {
            const searchTerm = document.getElementById('search').value.toLowerCase();
            const languageFilter = document.getElementById('filterLanguage').value;
            
            const filtered = snippets.filter(snippet => {
                const matchesSearch = snippet.title.toLowerCase().includes(searchTerm) ||
                                    snippet.code.toLowerCase().includes(searchTerm);
                const matchesLanguage = !languageFilter || snippet.language === languageFilter;
                return matchesSearch && matchesLanguage;
            });

            const html = filtered.map(snippet => `
                <div class="snippet-card">
                    <h3 style="color: var(--hb-blue); margin: 0 0 8px;">${snippet.title}</h3>
                    <p style="color: #666;">
                        <strong style="color: var(--hb-orange);">${snippet.language}</strong> 
                        • 📅 ${snippet.date}
                    </p>
                    <pre>${snippet.code}</pre>
                    <div style="margin-top: 12px;">
                        ${snippet.tags.map(t => `<span class="tag">#${t}</span>`).join(' ')}
                    </div>
                    <button class="danger" onclick="deleteSnippet(${snippet.id})" 
                            style="margin-top: 15px;">
                        🗑️ Delete
                    </button>
                </div>
            `).join('');

            document.getElementById('snippetsContainer').innerHTML = html || 
                '<p style="text-align: center; color: var(--hb-blue);">No snippets found. Start adding your code! ✨</p>';
        }

        window.deleteSnippet = (id) => {
            if (confirm('Delete this snippet permanently?')) {
                snippets = snippets.filter(s => s.id !== id);
                saveSnippets();
                renderSnippets();
            }
        }

        renderSnippets();
    </script>
</body>
</html>
