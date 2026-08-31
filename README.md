<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منصة مناقشة المشاريع</title>
    <style>
        :root {
            --primary-color: #4f46e5;
            --primary-hover: #4338ca;
            --bg-color: #f8fafc;
            --card-bg: #ffffff;
            --text-color: #1e293b;
            --border-color: #e2e8f0;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            line-height: 1.6;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
        }

        header h1 {
            color: var(--primary-color);
            font-size: 28px;
            margin-bottom: 5px;
        }

        header p {
            color: #64748b;
            font-size: 15px;
        }

        .card {
            background-color: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            margin-bottom: 25px;
        }

        .form-group {
            margin-bottom: 15px;
        }

        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            font-size: 14px;
        }

        input, textarea {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            font-size: 14px;
            outline: none;
            transition: border-color 0.2s;
        }

        input:focus, textarea:focus {
            border-color: var(--primary-color);
        }

        textarea {
            resize: vertical;
            height: 100px;
        }

        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 15px;
            font-weight: 600;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.2s;
            width: 100%;
        }

        button:hover {
            background-color: var(--primary-hover);
        }

        .projects-list h2 {
            font-size: 20px;
            margin-bottom: 15px;
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 5px;
        }

        .project-item {
            background-color: var(--card-bg);
            padding: 15px 20px;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
            margin-bottom: 15px;
            border-right: 4px solid var(--primary-color);
        }

        .project-item h3 {
            font-size: 18px;
            color: var(--text-color);
            margin-bottom: 5px;
        }

        .project-item p {
            color: #475569;
            font-size: 14px;
            margin-bottom: 10px;
        }

        .project-actions {
            display: flex;
            gap: 10px;
        }

        .project-actions button {
            background-color: #e2e8f0;
            color: var(--text-color);
            padding: 5px 12px;
            font-size: 13px;
            width: auto;
        }

        .project-actions button:hover {
            background-color: #cbd5e1;
        }
    </style>
</head>
<body>

    <div class="container">
        <header>
            <h1>منصة مناقشة المشاريع</h1>
            <p>شارك أفكارك ومشاريعك، واستقبل آراء المجتمع التطويري</p>
        </header>

        <div class="card">
            <h2 style="font-size: 18px; margin-bottom: 15px;">إضافة مشروع جديد</h2>
            <div class="form-group">
                <label for="projectTitle">عنوان المشروع</label>
                <input type="text" id="projectTitle" placeholder="أدخل عنوان المشروع...">
            </div>
            <div class="form-group">
                <label for="projectDesc">وصف المشروع أو فكرته</label>
                <textarea id="projectDesc" placeholder="اكتب تفاصيل المشروع والمشكلة التي يحلها..."></textarea>
            </div>
            <button id="addProjectBtn">نشر المشروع للمناقشة</button>
        </div>

        <div class="projects-list">
            <h2>المشاريع المطروحة للنقاش</h2>
            <div id="projectsContainer">
                </div>
        </div>
    </div>

    <script>
        const addProjectBtn = document.getElementById('addProjectBtn');
        const projectTitleInput = document.getElementById('projectTitle');
        const projectDescInput = document.getElementById('projectDesc');
        const projectsContainer = document.getElementById('projectsContainer');

        // مصفوفة تخزين المشاريع (افتراضية مع مشروع تجريبي)
        let projects = [
            {
                title: "تطبيق إدارة المهام الذكي",
                description: "منصة ويب تساعد الفرق على تنظيم المهام وتوزيعها باستخدام الذكاء الاصطناعي."
            }
        ];

        // دالة لعرض المشاريع على الشاشة
        function renderProjects() {
            projectsContainer.innerHTML = '';
            
            if (projects.length === 0) {
                projectsContainer.innerHTML = '<p style="color: #64748b; text-align: center;">لا توجد مشاريع مضافة حتى الآن.</p>';
                return;
            }

            projects.forEach((project, index) => {
                const projectElement = document.createElement('div');
                projectElement.classList.add('project-item');
                
                projectElement.innerHTML = `
                    <h3>${escapeHtml(project.title)}</h3>
                    <p>${escapeHtml(project.description)}</p>
                    <div class="project-actions">
                        <button onclick="discussProject(${index})">💬 مناقشة</button>
                        <button onclick="deleteProject(${index})" style="background-color: #fee2e2; color: #991b1b;">حذف</button>
                    </div>
                `;
                
                projectsContainer.appendChild(projectElement);
            });
        }

        // دالة إضافة مشروع جديد
        addProjectBtn.addEventListener('click', function() {
            const title = projectTitleInput.value.trim();
            const description = projectDescInput.value.trim();

            if (title === '' || description === '') {
                alert('الرجاء تعبئة جميع الحقول قبل النشر!');
                return;
            }

            projects.unshift({ title, description }); // إضافة المشروع في البداية
            projectTitleInput.value = '';
            projectDescInput.value = '';
            
            renderProjects();
        });

        // دالة حذف مشروع
        function deleteProject(index) {
            projects.splice(index, 1);
            renderProjects();
        }

        // دالة تفاعلية للمناقشة
        function discussProject(index) {
            const comment = prompt(`اكتب تعليقك أو سؤالك حول مشروع: "${projects[index].title}"`);
            if (comment) {
                alert('تم إضافة تعليقك بنجاح للمناقشة!');
            }
        }

        // حماية أمان النصوص البسيطة
        function escapeHtml(text) {
            const map = {
                '&': '&amp;',
                '<': '&lt;',
                '>': '&gt;',
                '"': '&quot;',
                "'": '&#039;'
            };
            return text.replace(/[&<>"']/g, function(m) { return map[m]; });
        }

        // تشغيل العرض الأولي عند فتح الصفحة
        renderProjects();
    </script>

</body>
</html>
