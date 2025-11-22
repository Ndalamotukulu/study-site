# study-site
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Tukulu Study Notes</title>
  <title>Tukulu Edu</title>
  
  <style>
    :root{
      --bg:#0b0b0b; --card:#111; --accent:#e63946; --muted:#9aa3b2; --glass: rgba(255,255,255,0.03);
    }
    body{
      margin:0; font-family:Inter, system-ui, Arial, sans-serif; background:linear-gradient(180deg,#020202 0%, #071017 100%); color:#fff;
    }
    .wrap{max-width:980px;margin:28px auto;padding:20px;}
    header{display:flex;align-items:center;gap:16px;margin-bottom:20px}
    h1{margin:0;font-size:20px}
    .urow{display:flex;gap:12px;flex-wrap:wrap;align-items:center}
    label{font-size:13px;color:var(--muted)}
    input[type="text"], select, input[type="file"]{
      background:var(--glass); border:1px solid rgba(255,255,255,0.03); padding:10px;border-radius:8px;color:#fff;
    }
    button{background:var(--accent);border:none;padding:10px 14px;border-radius:8px;color:#fff;cursor:pointer}
    .card{background:var(--card);border-radius:10px;padding:14px;margin-top:16px;box-shadow:0 6px 18px rgba(0,0,0,0.6)}
    .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:12px;margin-top:12px}
    .note{background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent); padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.02)}
    .note h3{margin:0;font-size:16px}
    .note p{margin:6px 0 0;color:var(--muted);font-size:13px}
    .note a{display:inline-block;margin-top:8px;color:#9fe7ff;text-decoration:underline}
    .meta{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px}
    .chip{background:rgba(255,255,255,0.03);padding:6px 8px;border-radius:999px;font-size:12px;color:var(--muted)}
    .controls{display:flex;gap:10px;align-items:center;margin-top:12px;flex-wrap:wrap}
    .small{font-size:13px;color:var(--muted)}
    .progress{height:8px;background:rgba(255,255,255,0.04);border-radius:6px;overflow:hidden;margin-top:8px}
    .progress > i{display:block;height:100%;background:linear-gradient(90deg,#7ad0ff,#00c2a8);width:0%}
    footer{margin-top:28px;color:var(--muted);font-size:13px;text-align:center}
    @media (max-width:520px){header{flex-direction:column;align-items:flex-start}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>Study Notes Library</h1>
        <div class="small">Upload and browse PDF (pdk) notes — subjects, grades, search</div>
      </div>
    </header>

    <!-- Upload card -->
    <div class="card" id="uploadCard">
      <div class="urow">
        <div style="flex:1">
          <label>Lesson Title</label><br/>
          <input id="title" type="text" placeholder="e.g. Mathematics - Trigonometry" />
        </div>

        <div style="width:160px">
          <label>Subject</label><br/>
          <select id="subject">
            <option value="">-- Subject --</option>
            <option>Mathematics</option>
            <option>Physical Science</option>
            <option>Life Sciences</option>
            <option>English</option>
            <option>History</option>
            <option>Accounting</option>
            <option>Computer Science</option>
            <option>Business Studies</option>
            <option>Geography</option>
            <option>Math Lit</option>
            <option>Other</option>
          </select>
        </div>

        <div style="width:140px">
          <label>Grade</label><br/>
          <select id="grade">
            <option value="">-- Grade --</option>
           
            <option>Grade 9</option>
            <option>Grade 10</option>
            <option>Grade 11</option>
            <option>Grade 12</option>
            <option>Beginner</option>
            <option>iQ</option>
            <option>Advanced</option>
          </select>
        </div>
      </div>

      <div class="urow" style="margin-top:12px">
        <div style="flex:1">
          <label>Short description (optional)</label><br/>
          <input id="description" type="text" placeholder="A short note about the file" />
        </div>

        <div style="width:220px">
          <label>Upload PDF (pdk)</label><br/>
          <input id="pdf" type="file" accept="application/pdf" />
        </div>

        <div style="align-self:flex-end">
          <button id="uploadBtn">Upload Note</button>
        </div>
      </div>

      <div id="uploadStatus" class="small" style="margin-top:10px"></div>
      <div class="progress" style="display:none;margin-top:10px"><i id="progBar"></i></div>
    </div>

    <!-- Filters -->
    <div class="card">
      <div style="display:flex;gap:12px;align-items:center;justify-content:space-between;flex-wrap:wrap">
        <div class="urow" style="gap:8px">
          <input id="search" type="text" placeholder="Search title or description" style="padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:#fff" />
          <select id="filterSubject">
            <option value="">All subjects</option>
            <option>Mathematics</option><option>Physical Science</option><option>Life Sciences</option>
            <option>English</option><option>History</option><option>Accounting</option>
            <option>Computer Science</option><option>Business Studies</option><option>Geography</option><option>Other</option>
          </select>
          <select id="filterGrade">
            <option value="">All grades</option>
            <option>Grade 8</option><option>Grade 9</option><option>Grade 10</option><option>Grade 11</option><option>Grade 12</option>
            <option>Beginner</option><option>Intermediate</option><option>Advanced</option>
          </select>
          <button id="clearFilters">Clear</button>
        </div>
        <div class="small">Notes are stored on Firebase Storage + Firestore</div>
      </div>
    </div>

    <!-- Notes list -->
    <div id="notesContainer" class="grid" style="margin-top:14px"></div>

    <footer>
      Tip: For production, secure uploads with Firebase Authentication or a server-side function. See instructions below.
    </footer>
  </div>

  <!-- Firebase + App script -->
  <script type="module">
    // ---------------------------
    // Replace firebaseConfig with your project keys if different
    // ---------------------------
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-app.js";
    import {
      getFirestore, collection, addDoc, getDocs, query, orderBy
    } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-firestore.js";
    import {
      getStorage, ref as sref, uploadBytesResumable, getDownloadURL
    } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-storage.js";

    const firebaseConfig = {
      apiKey: "AIzaSyAg6QVwlLj1MJ25eiD1caeF_S21k_gzfXU",
      authDomain: "tukuluflix.firebaseapp.com",
      projectId: "tukuluflix",
      storageBucket: "tukuluflix.firebasestorage.app",
      messagingSenderId: "615822572297",
      appId: "1:615822572297:web:9586daf4a4df2c034bf1d8",
      measurementId: "G-H6GNWLR236"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const storage = getStorage(app);

    // DOM
    const titleInput = document.getElementById("title");
    const subjectInput = document.getElementById("subject");
    const gradeInput = document.getElementById("grade");
    const descInput = document.getElementById("description");
    const pdfInput = document.getElementById("pdf");
    const uploadBtn = document.getElementById("uploadBtn");
    const uploadStatus = document.getElementById("uploadStatus");
    const progBar = document.getElementById("progBar");
    const notesContainer = document.getElementById("notesContainer");

    const searchInput = document.getElementById("search");
    const filterSubject = document.getElementById("filterSubject");
    const filterGrade = document.getElementById("filterGrade");
    const clearFilters = document.getElementById("clearFilters");

    // Upload handler
    uploadBtn.addEventListener("click", async () => {
      const title = titleInput.value.trim();
      const subject = subjectInput.value;
      const grade = gradeInput.value;
      const description = descInput.value.trim();
      const file = pdfInput.files[0];

      if (!title || !subject || !grade || !file) {
        uploadStatus.textContent = "Please fill title, subject, grade and choose a PDF.";
        return;
      }

      uploadStatus.textContent = "Preparing upload...";
      progBar.style.width = "0%";
      document.querySelector('.progress').style.display = "block";

      try {
        // create a storage ref
        const fileRef = sref(storage, `notes/${Date.now()}_${file.name}`);
        const uploadTask = uploadBytesResumable(fileRef, file);

        uploadTask.on('state_changed', (snapshot) => {
          const pct = Math.round((snapshot.bytesTransferred / snapshot.totalBytes) * 100);
          progBar.style.width = pct + "%";
        });

        // wait until upload finishes
        await new Promise((res, rej) => {
          uploadTask.on('state_changed', null, err => rej(err), () => res());
        });

        const url = await getDownloadURL(fileRef);

        // save metadata to Firestore
        await addDoc(collection(db, "notes"), {
          title, subject, grade, description,
          pdfUrl: url, created: new Date().toISOString()
        });

        uploadStatus.textContent = "Uploaded successfully!";
        titleInput.value = ""; subjectInput.value = ""; gradeInput.value = ""; descInput.value = ""; pdfInput.value = "";
        progBar.style.width = "0%";
        document.querySelector('.progress').style.display = "none";

        // refresh list
        loadNotes();
      } catch (err) {
        console.error(err);
        uploadStatus.textContent = "Upload failed: " + (err.message || err);
        document.querySelector('.progress').style.display = "none";
      }
    });

    // Load notes
    async function loadNotes() {
      notesContainer.innerHTML = ""; // clear
      // order by created if available
      const q = query(collection(db, "notes"), orderBy("created", "desc"));
      const snap = await getDocs(q);
      const list = [];
      snap.forEach(doc => {
        list.push({ id: doc.id, ...doc.data() });
      });

      renderNotes(list);
    }

    // Render notes with current filters
    function renderNotes(notes) {
      const search = (searchInput.value || "").toLowerCase();
      const subj = filterSubject.value;
      const grade = filterGrade.value;

      const filtered = notes.filter(n => {
        if (subj && n.subject !== subj) return false;
        if (grade && n.grade !== grade) return false;
        if (search) {
          const combined = (n.title + " " + (n.description || "") + " " + (n.subject || "")).toLowerCase();
          return combined.includes(search);
        }
        return true;
      });

      if (filtered.length === 0) {
        notesContainer.innerHTML = `<div style="grid-column:1/-1;color:var(--muted);padding:10px">No notes found</div>`;
        return;
      }

      filtered.forEach(n => {
        const div = document.createElement('div');
        div.className = 'note';
        div.innerHTML = `
          <h3>${escapeHtml(n.title)}</h3>
          <p>${escapeHtml(n.description || "")}</p>
          <div class="meta">
            <div class="chip">${escapeHtml(n.subject)}</div>
            <div class="chip">${escapeHtml(n.grade)}</div>
            <div class="chip small">Uploaded: ${new Date(n.created).toLocaleString()}</div>
          </div>
          <a href="${n.pdfUrl}" target="_blank" rel="noopener">Open PDF</a>
        `;
        notesContainer.appendChild(div);
      });
    }

    // utilities
    function escapeHtml(text) {
      return text
        .replaceAll('&','&amp;')
        .replaceAll('<','&lt;')
        .replaceAll('>','&gt;')
        .replaceAll('"','&quot;')
        .replaceAll("'",'&#039;');
    }

    // search & filters events
    [searchInput, filterSubject, filterGrade].forEach(el => el.addEventListener('input', () => loadNotesDebounced()));
    clearFilters.addEventListener('click', () => {
      searchInput.value = ""; filterSubject.value = ""; filterGrade.value = ""; loadNotes();
    });

    // simple debounce
    let deb = null;
    function loadNotesDebounced(){
      clearTimeout(deb);
      deb = setTimeout(loadNotes, 300);
    }

    // initial load
    loadNotes();

    // ---------------------------
    // FIREBASE RULES (development)
    // ---------------------------
    // For testing only (unsecure): In Firebase Console -> Firestore rules, you can set:
    //
    // service cloud.firestore {
    //   match /databases/{database}/documents {
    //     match /{document=**} {
    //       allow read, write: if true;
    //     }
    //   }
    // }
    //
    // And for Storage (unsecure):
    //
    // rules_version = '2';
    // service firebase.storage {
    //   match /b/{bucket}/o {
    //     match /{allPaths=**} {
    //       allow read, write: if true;
    //     }
    //   }
    // }
    //
    // IMPORTANT: This makes your project public. For production, require authentication or use a server function to handle uploads.
  </script>
</body>
</html>
