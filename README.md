import { useState, useRef, useCallback } from "react";
import {
  LayoutDashboard, Calendar, UserCheck, ClipboardList, Settings,
  Bell, Search, ChevronRight, Plus, X, Clock, AlertTriangle,
  Users, FileText, Mail, Phone, Edit3, Trash2,
  Star, AlertCircle, CheckCircle2, Circle,
  CalendarDays, LogOut, Shield, Activity, Zap,
  Upload, Send, Paperclip, XCircle, Lock, Unlock,
  RefreshCw, Download, BarChart2, Eye, EyeOff,
  UserPlus, Building, MapPin, Link, ChevronDown
} from "lucide-react";

// ─────────────────────────────────────────────
// USERS (for Login)
// ─────────────────────────────────────────────
const USER_DB = [
  { id: "U001", name: "Admin User",   email: "admin@company.com",        password: "admin123",     role: "Admin",     dept: "HR",  avatar: "AU" },
  { id: "U002", name: "Priya Nair",   email: "priya.nair@company.com",   password: "recruiter1",   role: "Recruiter", dept: "TA",  avatar: "PN" },
  { id: "U003", name: "Amit Joshi",   email: "amit.joshi@company.com",   password: "recruiter2",   role: "Recruiter", dept: "TA",  avatar: "AJ" },
  { id: "U004", name: "Deepak Mehta", email: "deepak.mehta@company.com", password: "hrbp1",        role: "HRBP",      dept: "HR",  avatar: "DM" },
  { id: "U005", name: "Kavya Reddy",  email: "kavya.reddy@company.com",  password: "hrbp2",        role: "HRBP",      dept: "HR",  avatar: "KR" },
];

// ─────────────────────────────────────────────
// CONSTANTS & MOCK DATA
// ─────────────────────────────────────────────
const JD_LIBRARY = {
  "Senior Product Designer": { fileName: "JD_Senior_Product_Designer.pdf", content: "Senior Product Designer with 5+ years UX/UI experience, Figma proficiency, strong portfolio." },
  "Backend Engineer":        { fileName: "JD_Backend_Engineer.pdf",        content: "Backend Engineer with Node.js/Python, microservices, AWS/GCP. 4+ years required." },
  "Data Analyst":            { fileName: "JD_Data_Analyst.pdf",            content: "Data Analyst skilled in SQL, Python (Pandas/NumPy), Tableau/Power BI." },
  "DevOps Engineer":         { fileName: "JD_DevOps_Engineer.pdf",         content: "DevOps Engineer with CI/CD, Docker, Kubernetes, Terraform experience." },
  "HR Business Partner":     { fileName: "JD_HR_Business_Partner.pdf",     content: "HRBP with strong stakeholder management, performance management expertise." },
  "Full Stack Developer":    { fileName: "JD_Full_Stack_Developer.pdf",    content: "Full Stack Developer: React, Node.js, PostgreSQL, REST APIs. 3+ years." },
};

const INTERVIEWERS_DB = [
  { name: "Rohan Mehta",  email: "rohan.mehta@company.com" },
  { name: "Sneha Iyer",   email: "sneha.iyer@company.com" },
  { name: "Vikram Singh", email: "vikram.singh@company.com" },
  { name: "Anjali Gupta", email: "anjali.gupta@company.com" },
  { name: "Deepa Rao",    email: "deepa.rao@company.com" },
];

const RECRUITERS_DB = [
  { name: "Priya Nair",   email: "priya.nair@company.com" },
  { name: "Amit Joshi",   email: "amit.joshi@company.com" },
  { name: "Neha Sinha",   email: "neha.sinha@company.com" },
];

const HRBP_DB = [
  { name: "Deepak Mehta", email: "deepak.mehta@company.com" },
  { name: "Kavya Reddy",  email: "kavya.reddy@company.com" },
  { name: "Roopa Bhat",   email: "roopa.bhat@company.com" },
];

const INTERVIEW_LEVELS = [
  "L1 - Screening", "L2 - Technical", "L3 - System Design",
  "L4 - Managerial", "L5 - Leadership / HR Final",
];

const STATUS_LIST = ["All", "Scheduled", "Completed", "Rescheduled", "Rejected"];

const DEPARTMENTS = ["Engineering","Product","Design","Sales","Finance","Marketing","Operations","HR","Legal"];
const LOCATIONS   = ["Hyderabad","Bangalore","Mumbai","Chennai","Delhi","Pune","Kolkata"];
const SOURCES     = ["LinkedIn","Naukri","Referral","Campus","Job Portal","Walk-in"];

// helper: days offset from a base date string
function daysOffset(base, n) {
  const d = new Date(base);
  d.setDate(d.getDate() + n);
  return d.toISOString().split("T")[0];
}

const TODAY_STR = "2026-05-21";

const INITIAL_INTERVIEWS = [
  { id:1, candidate:"Priya Sharma",       email:"priya@email.com",   role:"Senior Product Designer", recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", interviewerName:"Rohan Mehta",  interviewerEmail:"rohan.mehta@company.com", date:"2026-05-22", time:"10:00", link:"https://zoom.us/j/123",             status:"Scheduled",  level:"L2 - Technical",        resumeName:null,                      rounds:[], inviteSent:false, rejectionEmailSent:false },
  { id:2, candidate:"Arjun Nair",         email:"arjun@email.com",   role:"Backend Engineer",        recruiterName:"Amit Joshi",   recruiterEmail:"amit.joshi@company.com",   hrbpName:"Kavya Reddy",  hrbpEmail:"kavya.reddy@company.com",  interviewerName:"Sneha Iyer",   interviewerEmail:"sneha.iyer@company.com",  date:"2026-05-22", time:"14:00", link:"https://teams.microsoft.com/1", status:"Scheduled",  level:"L1 - Screening",        resumeName:null,                      rounds:[], inviteSent:false, rejectionEmailSent:false },
  { id:3, candidate:"Kavita Reddy",       email:"kavita@email.com",  role:"Data Analyst",            recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", interviewerName:"Vikram Singh", interviewerEmail:"vikram.singh@company.com",date:"2026-05-20", time:"11:00", link:"https://zoom.us/j/456",             status:"Completed",  level:"L2 - Technical",        resumeName:"Kavita_Reddy_CV.pdf",     rounds:[{roundNum:1,level:"L1 - Screening",interviewer:"Anjali Gupta",score:4,notes:"Good communication. Strong SQL.",date:"2026-05-15",sharedWithNext:true},{roundNum:2,level:"L2 - Technical",interviewer:"Vikram Singh",score:4,notes:"Strong analytical. Recommend next round.",date:"2026-05-20",sharedWithNext:false}], inviteSent:true,  rejectionEmailSent:false },
  { id:4, candidate:"Mohammed Al-Rashid", email:"mo@email.com",      role:"DevOps Engineer",         recruiterName:"Amit Joshi",   recruiterEmail:"amit.joshi@company.com",   hrbpName:"Kavya Reddy",  hrbpEmail:"kavya.reddy@company.com",  interviewerName:"Anjali Gupta", interviewerEmail:"anjali.gupta@company.com",date:"2026-05-24", time:"15:30", link:"https://zoom.us/j/789",             status:"Rescheduled",level:"L3 - System Design",    resumeName:null,                      rounds:[], inviteSent:false, rejectionEmailSent:false },
  { id:5, candidate:"Sonal Kapoor",       email:"sonal@email.com",   role:"HR Business Partner",     recruiterName:"Neha Sinha",   recruiterEmail:"neha.sinha@company.com",   hrbpName:"Roopa Bhat",   hrbpEmail:"roopa.bhat@company.com",   interviewerName:"Deepa Rao",    interviewerEmail:"deepa.rao@company.com",   date:"2026-05-18", time:"09:00", link:"https://zoom.us/j/321",             status:"Rejected",   level:"L1 - Screening",        resumeName:null,                      rounds:[{roundNum:1,level:"L1 - Screening",interviewer:"Deepa Rao",score:2,notes:"Does not meet minimum experience.",date:"2026-05-18",sharedWithNext:false}], inviteSent:true, rejectionEmailSent:false },
  { id:6, candidate:"Tarun Bhatia",       email:"tarun@email.com",   role:"Full Stack Developer",    recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", interviewerName:"Rohan Mehta",  interviewerEmail:"rohan.mehta@company.com", date:"2026-05-23", time:"16:00", link:"https://zoom.us/j/654",             status:"Scheduled",  level:"L2 - Technical",        resumeName:null,                      rounds:[], inviteSent:false, rejectionEmailSent:false },
];

const MOCK_CANDIDATES = [
  { id:1, name:"Kavita Reddy", role:"Data Analyst",        stage:"Background Check",   offerDate:"2026-05-10", joiningDate:"2026-06-02", progress:65, recruiterName:"Priya Nair",   hrbpName:"Deepak Mehta", tasks:[{label:"Signed Offer Letter",status:"Verified"},{label:"Background Check",status:"Submitted"},{label:"ID Proof",status:"Verified"},{label:"Education Certificates",status:"Pending"},{label:"Bank Details Form",status:"Pending"}], touchpoints:[{date:"2026-05-15",type:"Call",note:"Welcomed her and discussed joining logistics."},{date:"2026-05-18",type:"Email",note:"Sent pre-boarding checklist and IT setup form."}], avatar:"KR" },
  { id:2, name:"Tarun Bhatia", role:"Full Stack Developer", stage:"Document Collection", offerDate:"2026-05-12", joiningDate:"2026-06-09", progress:30, recruiterName:"Priya Nair",   hrbpName:"Kavya Reddy",  tasks:[{label:"Signed Offer Letter",status:"Verified"},{label:"Background Check",status:"Pending"},{label:"ID Proof",status:"Submitted"},{label:"Education Certificates",status:"Pending"},{label:"Bank Details Form",status:"Pending"}], touchpoints:[{date:"2026-05-13",type:"Email",note:"Sent offer acceptance confirmation email."}], avatar:"TB" },
  { id:3, name:"Neha Joshi",   role:"Marketing Manager",   stage:"Ready for Day 1",    offerDate:"2026-04-28", joiningDate:"2026-05-26", progress:95, recruiterName:"Neha Sinha",   hrbpName:"Roopa Bhat",   tasks:[{label:"Signed Offer Letter",status:"Verified"},{label:"Background Check",status:"Verified"},{label:"ID Proof",status:"Verified"},{label:"Education Certificates",status:"Verified"},{label:"Bank Details Form",status:"Submitted"}], touchpoints:[{date:"2026-05-01",type:"Call",note:"Discussed role expectations."},{date:"2026-05-08",type:"Email",note:"Sent Day 1 schedule."},{date:"2026-05-16",type:"Call",note:"Quick check-in — confirmed joining."}], avatar:"NJ" },
];

// Probation data enriched from uploaded file
const MOCK_EMPLOYEES = [
  { id:"C001", empId:"EMP001", name:"Ananya Sharma",  dept:"Engineering", location:"Hyderabad", source:"LinkedIn", doj:daysOffset(TODAY_STR,-185), probationEnd:daysOffset(TODAY_STR,5),   reviewStatus:"Pending HRBP Confirmation", manager:"Rohan Mehta", recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", notifStatus:"Sent",         reviewLog:null },
  { id:"C002", empId:"EMP002", name:"Ravi Kumar",     dept:"Product",     location:"Bangalore", source:"Referral", doj:daysOffset(TODAY_STR,-180), probationEnd:daysOffset(TODAY_STR,0),   reviewStatus:"Due for Review",            manager:"Sneha Iyer",   recruiterName:"Amit Joshi",   recruiterEmail:"amit.joshi@company.com",   hrbpName:"Kavya Reddy",  hrbpEmail:"kavya.reddy@company.com",  notifStatus:"Not Triggered", reviewLog:null },
  { id:"C003", empId:"EMP003", name:"Meena Iyer",     dept:"Design",      location:"Mumbai",    source:"Naukri",   doj:daysOffset(TODAY_STR,-200), probationEnd:daysOffset(TODAY_STR,-20),  reviewStatus:"Completed",                 manager:"Vikram Singh", recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", notifStatus:"Sent",         reviewLog:{type:"90-Day", decision:"Confirm Employment", notes:"Performed exceptionally well."} },
  { id:"C004", empId:"EMP004", name:"Arjun Nambiar",  dept:"Sales",       location:"Chennai",   source:"Referral", doj:daysOffset(TODAY_STR,-210), probationEnd:daysOffset(TODAY_STR,60),   reviewStatus:"Extended",                  manager:"Anjali Gupta", recruiterName:"Amit Joshi",   recruiterEmail:"amit.joshi@company.com",   hrbpName:"Kavya Reddy",  hrbpEmail:"kavya.reddy@company.com",  notifStatus:"Sent",         reviewLog:{type:"60-Day", decision:"Extend Probation",   notes:"Needs mentoring for 3 more months."} },
  { id:"C005", empId:"EMP005", name:"Pooja Gupta",    dept:"Finance",     location:"Delhi",     source:"Campus",   doj:daysOffset(TODAY_STR,-90),  probationEnd:daysOffset(TODAY_STR,90),   reviewStatus:"Under Probation",           manager:"Deepa Rao",    recruiterName:"Priya Nair",   recruiterEmail:"priya.nair@company.com",   hrbpName:"Deepak Mehta", hrbpEmail:"deepak.mehta@company.com", notifStatus:"Not Triggered", reviewLog:null },
  { id:"C006", empId:"EMP006", name:"Vikram Singh",   dept:"Engineering", location:"Hyderabad", source:"LinkedIn", doj:daysOffset(TODAY_STR,-45),  probationEnd:daysOffset(TODAY_STR,135),  reviewStatus:"Under Probation",           manager:"Rohan Mehta",  recruiterName:"Amit Joshi",   recruiterEmail:"amit.joshi@company.com",   hrbpName:"Kavya Reddy",  hrbpEmail:"kavya.reddy@company.com",  notifStatus:"Not Triggered", reviewLog:null },
];

// ─────────────────────────────────────────────
// HELPERS
// ─────────────────────────────────────────────
function daysUntil(dateStr) {
  const d = new Date(dateStr), t = new Date(TODAY_STR);
  return Math.ceil((d - t) / 86400000);
}
function formatDate(d) {
  if (!d) return "—";
  return new Date(d).toLocaleDateString("en-IN", { day:"numeric", month:"short", year:"numeric" });
}
function fmtTime(t) {
  if (!t) return "";
  const [h,m] = t.split(":");
  const hr = parseInt(h);
  return `${hr%12||12}:${m} ${hr>=12?"PM":"AM"}`;
}
function initials(name) {
  return name.split(" ").map(w=>w[0]).join("").slice(0,2).toUpperCase();
}
function csvEscape(v) { return `"${String(v).replace(/"/g,'""')}"`; }

const STATUS_COLORS = {
  Scheduled:   "bg-blue-500/15 text-blue-400 border border-blue-500/30",
  Completed:   "bg-emerald-500/15 text-emerald-400 border border-emerald-500/30",
  Rescheduled: "bg-amber-500/15 text-amber-400 border border-amber-500/30",
  Rejected:    "bg-red-500/15 text-red-400 border border-red-500/30",
};
const TASK_CLR = { Verified:"text-emerald-400", Submitted:"text-blue-400", Pending:"text-slate-500" };

function TaskIcon({ status }) {
  if (status === "Verified")  return <CheckCircle2 size={14} className="text-emerald-400 flex-shrink-0" />;
  if (status === "Submitted") return <Clock size={14} className="text-blue-400 flex-shrink-0" />;
  return <Circle size={14} className="text-slate-600 flex-shrink-0" />;
}

// ─────────────────────────────────────────────
// EMAIL TEMPLATES
// ─────────────────────────────────────────────
function buildInviteTemplate(iv) {
  return `Subject: Interview Invitation – ${iv.role} | ${iv.level}

Dear ${iv.candidate},

We are pleased to invite you for an interview at PeopleCore for the role of ${iv.role}.

Interview Details:
• Round      : ${iv.level}
• Date       : ${formatDate(iv.date)}
• Time       : ${fmtTime(iv.time)}
• Meeting    : ${iv.link || "TBD"}
• Interviewer: ${iv.interviewerName}
${iv.resumeName ? `\nPlease keep your resume (${iv.resumeName}) handy.` : ""}
${JD_LIBRARY[iv.role] ? `\nJob Description is attached for reference.` : ""}

Please confirm your availability by replying to this email.

Best regards,
${iv.recruiterName || "HR Team"} | PeopleCore`;
}

function buildPanelTemplate(iv) {
  const sharedFb = iv.rounds.filter(r => r.sharedWithNext);
  return `Subject: Interview Assignment – ${iv.candidate} | ${iv.role} | ${iv.level}

Dear ${iv.interviewerName},

You have been assigned to interview a candidate for the ${iv.role} position.

Candidate Details:
• Name  : ${iv.candidate}
• Email : ${iv.email}
• Role  : ${iv.role}
• Round : ${iv.level}
• Date  : ${formatDate(iv.date)} at ${fmtTime(iv.time)}
• Link  : ${iv.link || "TBD"}

Attachments:
${iv.resumeName ? `• Resume: ${iv.resumeName}` : "• No resume uploaded yet."}
${JD_LIBRARY[iv.role] ? `• JD: ${JD_LIBRARY[iv.role].fileName}` : ""}

Assigned by Recruiter : ${iv.recruiterName || "—"} (${iv.recruiterEmail || "—"})
HRBP                  : ${iv.hrbpName || "—"} (${iv.hrbpEmail || "—"})
${sharedFb.length > 0 ? `\nPrior Round Feedback (shared):\n${sharedFb.map(r=>`• Round ${r.roundNum} (${r.level}) – Score: ${r.score}/5 – ${r.notes}`).join("\n")}` : ""}

Please log feedback in the HR system post-interview.

Best regards,
${iv.recruiterName || "HR Team"} | PeopleCore`;
}

function buildRejectionTemplate(iv) {
  return `Subject: Update on Your Application – ${iv.role}

Dear ${iv.candidate},

Thank you for interviewing with us for the ${iv.role} position at PeopleCore.

After careful consideration, we regret to inform you that we will not be moving forward with your application at this time.

We appreciate your interest and encourage you to apply for future openings that match your skills.

Wishing you all the best in your search.

Best regards,
${iv.recruiterName || "HR Team"} | PeopleCore`;
}

// ─────────────────────────────────────────────
// SHARED UI COMPONENTS
// ─────────────────────────────────────────────
function Avatar({ initials: ini, size="md", color="blue" }) {
  const sz = { sm:"w-7 h-7 text-xs", md:"w-9 h-9 text-sm", lg:"w-11 h-11 text-base" }[size];
  const cl = {
    blue:   "bg-blue-500/20 text-blue-300 border border-blue-500/30",
    emerald:"bg-emerald-500/20 text-emerald-300 border border-emerald-500/30",
    purple: "bg-purple-500/20 text-purple-300 border border-purple-500/30",
    amber:  "bg-amber-500/20 text-amber-300 border border-amber-500/30",
    red:    "bg-red-500/20 text-red-300 border border-red-500/30",
  }[color] || "bg-slate-500/20 text-slate-300 border border-slate-500/30";
  return <div className={`${sz} ${cl} rounded-full flex items-center justify-center font-semibold tracking-wide flex-shrink-0`}>{ini}</div>;
}

function Modal({ title, onClose, children, wide }) {
  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4" style={{ background:"rgba(5,10,20,0.88)", backdropFilter:"blur(6px)" }}>
      <div className={`w-full ${wide?"max-w-2xl":"max-w-lg"} bg-[#0f1623] border border-white/10 rounded-2xl shadow-2xl overflow-hidden`}>
        <div className="flex items-center justify-between px-6 py-4 border-b border-white/8">
          <h3 className="text-base font-semibold text-white">{title}</h3>
          <button onClick={onClose} className="text-slate-400 hover:text-white transition-colors p-1 rounded-lg hover:bg-white/8"><X size={18}/></button>
        </div>
        <div className="px-6 py-5 max-h-[82vh] overflow-y-auto">{children}</div>
      </div>
    </div>
  );
}

function Field({ label, children, half }) {
  return (
    <div className={`mb-4 ${half?"":"w-full"}`}>
      <label className="block text-[10px] font-semibold text-slate-400 mb-1.5 uppercase tracking-widest">{label}</label>
      {children}
    </div>
  );
}

const IC = "w-full bg-white/5 border border-white/10 rounded-xl px-3 py-2.5 text-sm text-white placeholder-slate-600 focus:outline-none focus:border-blue-500/60 focus:bg-white/8 transition-all";
const SC = "w-full bg-[#0a1020] border border-white/10 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500/60 transition-all";

function StatCard({ label, value, sub, icon:Icon, color="blue" }) {
  const m = {
    blue:   { bg:"bg-blue-500/10",   border:"border-blue-500/20",   icon:"text-blue-400",   val:"text-blue-300"   },
    emerald:{ bg:"bg-emerald-500/10", border:"border-emerald-500/20", icon:"text-emerald-400", val:"text-emerald-300" },
    amber:  { bg:"bg-amber-500/10",  border:"border-amber-500/20",  icon:"text-amber-400",  val:"text-amber-300"  },
    purple: { bg:"bg-purple-500/10", border:"border-purple-500/20", icon:"text-purple-400", val:"text-purple-300" },
    red:    { bg:"bg-red-500/10",    border:"border-red-500/20",    icon:"text-red-400",    val:"text-red-300"    },
  }[color];
  return (
    <div className={`${m.bg} border ${m.border} rounded-2xl p-5 flex items-start gap-4`}>
      <div className={`${m.bg} border ${m.border} rounded-xl p-2.5`}><Icon size={18} className={m.icon}/></div>
      <div>
        <p className={`text-2xl font-bold ${m.val} tracking-tight`}>{value}</p>
        <p className="text-sm font-medium text-white/80 mt-0.5">{label}</p>
        {sub && <p className="text-xs text-slate-500 mt-1">{sub}</p>}
      </div>
    </div>
  );
}

// Section divider for modal forms
function SectionDivider({ label }) {
  return (
    <div className="flex items-center gap-3 my-5">
      <div className="flex-1 h-px bg-white/8" />
      <span className="text-[10px] font-bold text-slate-500 uppercase tracking-widest">{label}</span>
      <div className="flex-1 h-px bg-white/8" />
    </div>
  );
}

// ─────────────────────────────────────────────
// REPORT GENERATOR
// ─────────────────────────────────────────────
function ReportsModule({ interviews, employees, candidates }) {
  const [reportType, setReportType] = useState("interviews");
  const [toast, setToast] = useState(null);

  function showToast(msg, ok=true) {
    setToast({ msg, ok });
    setTimeout(() => setToast(null), 3000);
  }

  function downloadCSV(rows, headers, filename) {
    const csv = [headers.join(","), ...rows.map(r => r.map(csvEscape).join(","))].join("\n");
    const a = document.createElement("a");
    a.href = URL.createObjectURL(new Blob([csv], { type:"text/csv" }));
    a.download = filename;
    a.click();
    showToast(`${filename} downloaded successfully.`);
  }

  function exportInterviews() {
    const headers = ["Candidate","Email","Role","Level","Interviewer","Panel Email","Recruiter","Recruiter Email","HRBP","HRBP Email","Date","Time","Status","Resume","Rounds","Invite Sent"];
    const rows = interviews.map(iv => [
      iv.candidate, iv.email, iv.role, iv.level,
      iv.interviewerName, iv.interviewerEmail,
      iv.recruiterName, iv.recruiterEmail,
      iv.hrbpName, iv.hrbpEmail,
      iv.date, fmtTime(iv.time), iv.status,
      iv.resumeName || "—", iv.rounds.length, iv.inviteSent ? "Yes" : "No",
    ]);
    downloadCSV(rows, headers, `interviews_report_${TODAY_STR}.csv`);
  }

  function exportProbation() {
    const headers = ["Employee ID","Name","Department","Location","Source","Date of Joining","Probation End","Days Left","Review Status","Manager","Recruiter","Recruiter Email","HRBP","HRBP Email","Notif Status","Decision","Notes"];
    const rows = employees.map(e => [
      e.empId, e.name, e.dept, e.location, e.source,
      formatDate(e.doj), formatDate(e.probationEnd), daysUntil(e.probationEnd),
      e.reviewStatus, e.manager,
      e.recruiterName, e.recruiterEmail, e.hrbpName, e.hrbpEmail,
      e.notifStatus,
      e.reviewLog?.decision || "—", e.reviewLog?.notes || "—",
    ]);
    downloadCSV(rows, headers, `probation_report_${TODAY_STR}.csv`);
  }

  function exportPreboarding() {
    const headers = ["Name","Role","Stage","Offer Date","Day 1","Progress %","Recruiter","HRBP","Pending Tasks","Touchpoints"];
    const rows = candidates.map(c => [
      c.name, c.role, c.stage, formatDate(c.offerDate), formatDate(c.joiningDate),
      c.progress, c.recruiterName, c.hrbpName,
      c.tasks.filter(t=>t.status==="Pending").map(t=>t.label).join("; ") || "None",
      c.touchpoints.length,
    ]);
    downloadCSV(rows, headers, `preboarding_report_${TODAY_STR}.csv`);
  }

  const tabs = [
    { id:"interviews",   label:"Interview Data",         icon:Calendar,     count:interviews.length },
    { id:"probation",    label:"Probation Tracker",      icon:ClipboardList, count:employees.length },
    { id:"preboarding",  label:"Pre-boarding Pipeline",  icon:UserCheck,    count:candidates.length },
  ];

  const summaryStats = {
    interviews: [
      { label:"Total",     value:interviews.length,                                          color:"blue" },
      { label:"Scheduled", value:interviews.filter(i=>i.status==="Scheduled").length,        color:"blue" },
      { label:"Completed", value:interviews.filter(i=>i.status==="Completed").length,        color:"emerald" },
      { label:"Rejected",  value:interviews.filter(i=>i.status==="Rejected").length,         color:"red" },
    ],
    probation: [
      { label:"Total",           value:employees.length,                                                   color:"blue" },
      { label:"Under Probation", value:employees.filter(e=>e.reviewStatus==="Under Probation").length,     color:"blue" },
      { label:"Completed",       value:employees.filter(e=>e.reviewStatus==="Completed").length,           color:"emerald" },
      { label:"Due/Pending",     value:employees.filter(e=>["Due for Review","Pending HRBP Confirmation"].includes(e.reviewStatus)).length, color:"amber" },
    ],
    preboarding: [
      { label:"Total",           value:candidates.length,                                                  color:"blue" },
      { label:"Ready for Day 1", value:candidates.filter(c=>c.stage==="Ready for Day 1").length,          color:"emerald" },
      { label:"In Progress",     value:candidates.filter(c=>c.stage!=="Ready for Day 1").length,          color:"amber" },
      { label:"Avg Progress",    value:`${Math.round(candidates.reduce((a,c)=>a+c.progress,0)/Math.max(candidates.length,1))}%`, color:"purple" },
    ],
  };

  return (
    <div className="p-8">
      {toast && (
        <div className={`fixed bottom-6 right-6 z-50 flex items-center gap-3 px-4 py-3 rounded-xl border text-sm font-medium shadow-2xl transition-all
          ${toast.ok ? "bg-emerald-900/90 border-emerald-500/40 text-emerald-300" : "bg-red-900/90 border-red-500/40 text-red-300"}`}>
          {toast.ok ? <CheckCircle2 size={16}/> : <AlertCircle size={16}/>} {toast.msg}
        </div>
      )}

      <div className="flex items-center justify-between mb-6">
        <div>
          <h2 className="text-base font-bold text-white">Reports & Export</h2>
          <p className="text-xs text-slate-500 mt-0.5">Download CSV reports for any module</p>
        </div>
      </div>

      {/* Tab bar */}
      <div className="flex gap-1 bg-white/5 border border-white/8 rounded-xl p-1 w-fit mb-6">
        {tabs.map(t => (
          <button key={t.id} onClick={() => setReportType(t.id)}
            className={`flex items-center gap-2 px-4 py-2 rounded-lg text-xs font-semibold transition-all
              ${reportType===t.id ? "bg-blue-600 text-white shadow-sm" : "text-slate-500 hover:text-slate-300"}`}>
            <t.icon size={13}/> {t.label}
            <span className={`text-[10px] px-1.5 py-0.5 rounded-full ${reportType===t.id ? "bg-white/20 text-white" : "bg-white/8 text-slate-500"}`}>{t.count}</span>
          </button>
        ))}
      </div>

      {/* Summary stats */}
      <div className="grid grid-cols-4 gap-3 mb-6">
        {summaryStats[reportType].map(s => (
          <div key={s.label} className={`p-4 rounded-2xl border bg-${s.color}-500/10 border-${s.color}-500/20`}>
            <p className={`text-xl font-bold text-${s.color}-300`}>{s.value}</p>
            <p className="text-xs text-slate-400 mt-0.5">{s.label}</p>
          </div>
        ))}
      </div>

      {/* Preview table */}
      <div className="bg-white/3 border border-white/8 rounded-2xl overflow-hidden mb-6">
        <div className="flex items-center justify-between px-5 py-3.5 border-b border-white/8">
          <p className="text-xs font-bold text-white uppercase tracking-wider">Data Preview</p>
          <button
            onClick={reportType==="interviews" ? exportInterviews : reportType==="probation" ? exportProbation : exportPreboarding}
            className="flex items-center gap-2 bg-emerald-600 hover:bg-emerald-500 text-white px-4 py-2 rounded-xl text-xs font-semibold transition-all shadow-lg shadow-emerald-600/20">
            <Download size={13}/> Download CSV
          </button>
        </div>

        {/* Interviews preview */}
        {reportType === "interviews" && (
          <div className="overflow-x-auto">
            <table className="w-full text-xs">
              <thead><tr className="border-b border-white/8 bg-white/3">
                {["Candidate","Role","Level","Interviewer","Recruiter","HRBP","Date","Status"].map(h => (
                  <th key={h} className="text-left px-4 py-2.5 text-[10px] font-bold text-slate-500 uppercase tracking-wider whitespace-nowrap">{h}</th>
                ))}
              </tr></thead>
              <tbody className="divide-y divide-white/5">
                {interviews.map(iv => (
                  <tr key={iv.id} className="hover:bg-white/3">
                    <td className="px-4 py-2.5 text-white font-medium">{iv.candidate}</td>
                    <td className="px-4 py-2.5 text-slate-400">{iv.role}</td>
                    <td className="px-4 py-2.5 text-blue-400">{iv.level}</td>
                    <td className="px-4 py-2.5 text-slate-400">{iv.interviewerName}</td>
                    <td className="px-4 py-2.5 text-purple-400">{iv.recruiterName}</td>
                    <td className="px-4 py-2.5 text-amber-400">{iv.hrbpName}</td>
                    <td className="px-4 py-2.5 text-slate-400">{formatDate(iv.date)}</td>
                    <td className="px-4 py-2.5"><span className={`text-[10px] font-semibold px-2 py-0.5 rounded-full ${STATUS_COLORS[iv.status]}`}>{iv.status}</span></td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        )}

        {/* Probation preview */}
        {reportType === "probation" && (
          <div className="overflow-x-auto">
            <table className="w-full text-xs">
              <thead><tr className="border-b border-white/8 bg-white/3">
                {["Employee","Dept","Probation End","Days Left","Review Status","Recruiter","HRBP","Decision"].map(h => (
                  <th key={h} className="text-left px-4 py-2.5 text-[10px] font-bold text-slate-500 uppercase tracking-wider whitespace-nowrap">{h}</th>
                ))}
              </tr></thead>
              <tbody className="divide-y divide-white/5">
                {employees.map(e => {
                  const d = daysUntil(e.probationEnd);
                  return (
                    <tr key={e.id} className="hover:bg-white/3">
                      <td className="px-4 py-2.5 text-white font-medium">{e.name}</td>
                      <td className="px-4 py-2.5 text-slate-400">{e.dept}</td>
                      <td className="px-4 py-2.5 text-slate-400">{formatDate(e.probationEnd)}</td>
                      <td className={`px-4 py-2.5 font-mono font-bold ${d<0?"text-red-400":d<=14?"text-red-400":d<=30?"text-amber-400":"text-emerald-400"}`}>
                        {d<0?`${Math.abs(d)}d ago`:`${d}d`}
                      </td>
                      <td className="px-4 py-2.5 text-slate-300">{e.reviewStatus}</td>
                      <td className="px-4 py-2.5 text-purple-400">{e.recruiterName}</td>
                      <td className="px-4 py-2.5 text-amber-400">{e.hrbpName}</td>
                      <td className="px-4 py-2.5 text-emerald-400">{e.reviewLog?.decision || "—"}</td>
                    </tr>
                  );
                })}
              </tbody>
            </table>
          </div>
        )}

        {/* Preboarding preview */}
        {reportType === "preboarding" && (
          <div className="overflow-x-auto">
            <table className="w-full text-xs">
              <thead><tr className="border-b border-white/8 bg-white/3">
                {["Candidate","Role","Stage","Offer Date","Day 1","Progress","Recruiter","HRBP"].map(h => (
                  <th key={h} className="text-left px-4 py-2.5 text-[10px] font-bold text-slate-500 uppercase tracking-wider whitespace-nowrap">{h}</th>
                ))}
              </tr></thead>
              <tbody className="divide-y divide-white/5">
                {candidates.map(c => (
                  <tr key={c.id} className="hover:bg-white/3">
                    <td className="px-4 py-2.5 text-white font-medium">{c.name}</td>
                    <td className="px-4 py-2.5 text-slate-400">{c.role}</td>
                    <td className="px-4 py-2.5 text-blue-400">{c.stage}</td>
                    <td className="px-4 py-2.5 text-slate-400">{formatDate(c.offerDate)}</td>
                    <td className="px-4 py-2.5 text-emerald-400 font-medium">{formatDate(c.joiningDate)}</td>
                    <td className="px-4 py-2.5">
                      <div className="flex items-center gap-2">
                        <div className="h-1.5 w-16 bg-white/8 rounded-full overflow-hidden">
                          <div className="h-full rounded-full" style={{width:`${c.progress}%`,background:c.progress>=90?"#34d399":c.progress>=60?"#60a5fa":"#f59e0b"}}/>
                        </div>
                        <span className="text-slate-400">{c.progress}%</span>
                      </div>
                    </td>
                    <td className="px-4 py-2.5 text-purple-400">{c.recruiterName}</td>
                    <td className="px-4 py-2.5 text-amber-400">{c.hrbpName}</td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        )}
      </div>
    </div>
  );
}

// ─────────────────────────────────────────────
// SIDEBAR
// ─────────────────────────────────────────────
function Sidebar({ active, setActive, currentUser, onLogout }) {
  const roleColor = { Admin:"text-blue-400", Recruiter:"text-purple-400", HRBP:"text-amber-400" };
  const navItems = [
    { id:"dashboard",   label:"Dashboard",             icon:LayoutDashboard },
    { id:"interviews",  label:"Interview Scheduler",   icon:Calendar },
    { id:"preboarding", label:"Post-Offer Engagement", icon:UserCheck },
    { id:"probation",   label:"Probation Tracker",     icon:ClipboardList },
    { id:"reports",     label:"Reports & Export",      icon:BarChart2 },
    { id:"settings",    label:"Settings",              icon:Settings },
  ];
  return (
    <aside className="w-60 flex-shrink-0 flex flex-col h-screen bg-[#080e1a] border-r border-white/6 sticky top-0">
      <div className="px-5 py-5 border-b border-white/6">
        <div className="flex items-center gap-3">
          <div className="w-8 h-8 rounded-xl bg-gradient-to-br from-blue-500 to-blue-700 flex items-center justify-center shadow-lg shadow-blue-500/30">
            <Shield size={15} className="text-white"/>
          </div>
          <div>
            <p className="text-sm font-bold text-white tracking-tight">PeopleCore</p>
            <p className="text-[10px] text-slate-500 font-medium tracking-widest uppercase">HR Suite</p>
          </div>
        </div>
      </div>
      <div className="px-5 py-4">
        <div className="flex items-center gap-2.5 bg-blue-500/8 border border-blue-500/20 rounded-xl px-3 py-2.5">
          <Avatar initials={currentUser.avatar} size="sm" color="blue"/>
          <div className="min-w-0">
            <p className="text-xs font-semibold text-white truncate">{currentUser.name}</p>
            <p className={`text-[10px] font-medium ${roleColor[currentUser.role] || "text-slate-400"}`}>{currentUser.role}</p>
          </div>
        </div>
      </div>
      <nav className="flex-1 px-3 py-2 space-y-0.5 overflow-y-auto">
        {navItems.map(({ id, label, icon:Icon }) => (
          <button key={id} onClick={() => setActive(id)}
            className={`w-full flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-medium transition-all text-left
              ${active===id ? "bg-blue-500/15 text-blue-300 border border-blue-500/25" : "text-slate-500 hover:text-slate-200 hover:bg-white/5"}`}>
            <Icon size={16} className={active===id ? "text-blue-400" : "text-slate-600"}/>
            {label}
            {active===id && <ChevronRight size={12} className="ml-auto text-blue-400/60"/>}
          </button>
        ))}
      </nav>
      <div className="px-3 py-4 border-t border-white/6">
        <button onClick={onLogout}
          className="w-full flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm text-slate-600 hover:text-red-400 hover:bg-red-500/8 transition-all">
          <LogOut size={15}/> Sign Out
        </button>
      </div>
    </aside>
  );
}

function Topbar({ title, subtitle }) {
  return (
    <div className="flex items-center justify-between px-8 py-5 border-b border-white/6 bg-[#090f1c]/80 backdrop-blur-sm sticky top-0 z-10">
      <div>
        <h1 className="text-lg font-bold text-white tracking-tight">{title}</h1>
        {subtitle && <p className="text-xs text-slate-500 mt-0.5">{subtitle}</p>}
      </div>
      <div className="flex items-center gap-3">
        <div className="relative">
          <Search size={14} className="absolute left-3 top-1/2 -translate-y-1/2 text-slate-600"/>
          <input placeholder="Search…" className="bg-white/5 border border-white/8 rounded-xl pl-8 pr-4 py-2 text-xs text-slate-300 placeholder-slate-600 focus:outline-none focus:border-blue-500/40 w-44 transition-all"/>
        </div>
        <button className="relative p-2 rounded-xl bg-white/5 border border-white/8 text-slate-500 hover:text-white transition-all">
          <Bell size={15}/>
          <span className="absolute top-1.5 right-1.5 w-1.5 h-1.5 bg-blue-500 rounded-full"/>
        </button>
      </div>
    </div>
  );
}

// ─────────────────────────────────────────────
// LOGIN SCREEN
// ─────────────────────────────────────────────
function LoginScreen({ onLogin }) {
  const [email, setEmail]       = useState("");
  const [password, setPassword] = useState("");
  const [showPw, setShowPw]     = useState(false);
  const [error, setError]       = useState("");
  const [loading, setLoading]   = useState(false);

  function handleSubmit(e) {
    e.preventDefault();
    setLoading(true);
    setError("");
    setTimeout(() => {
      const user = USER_DB.find(u => u.email.toLowerCase() === email.toLowerCase() && u.password === password);
      if (user) { onLogin(user); }
      else { setError("Invalid email or password. Please try again."); }
      setLoading(false);
    }, 600);
  }

  return (
    <div className="min-h-screen bg-[#090f1c] flex items-center justify-center p-4" style={{ fontFamily:"'DM Sans','Inter',system-ui,sans-serif" }}>
      <style>{`
        * { scrollbar-width:thin; scrollbar-color:rgba(255,255,255,0.1) transparent; }
        ::-webkit-scrollbar { width:4px; } ::-webkit-scrollbar-thumb { background:rgba(255,255,255,0.1); border-radius:99px; }
        input[type="date"]::-webkit-calendar-picker-indicator,
        input[type="time"]::-webkit-calendar-picker-indicator { filter:invert(0.5); }
        select option { background:#0f1623; }
      `}</style>

      <div className="w-full max-w-md">
        {/* Logo */}
        <div className="text-center mb-8">
          <div className="w-14 h-14 mx-auto rounded-2xl bg-gradient-to-br from-blue-500 to-blue-700 flex items-center justify-center shadow-2xl shadow-blue-500/30 mb-4">
            <Shield size={24} className="text-white"/>
          </div>
          <h1 className="text-2xl font-bold text-white tracking-tight">PeopleCore</h1>
          <p className="text-sm text-slate-500 mt-1">HR Suite — Sign in to continue</p>
        </div>

        {/* Form */}
        <div className="bg-[#0f1623] border border-white/10 rounded-2xl shadow-2xl p-8">
          <form onSubmit={handleSubmit}>
            <Field label="Email Address">
              <input className={IC} type="email" placeholder="you@company.com" value={email}
                onChange={e => setEmail(e.target.value)} required/>
            </Field>
            <Field label="Password">
              <div className="relative">
                <input className={`${IC} pr-10`} type={showPw ? "text" : "password"} placeholder="Enter your password"
                  value={password} onChange={e => setPassword(e.target.value)} required/>
                <button type="button" onClick={() => setShowPw(p=>!p)}
                  className="absolute right-3 top-1/2 -translate-y-1/2 text-slate-500 hover:text-slate-300 transition-colors">
                  {showPw ? <EyeOff size={15}/> : <Eye size={15}/>}
                </button>
              </div>
            </Field>

            {error && (
              <div className="flex items-center gap-2 text-xs text-red-400 bg-red-500/10 border border-red-500/20 rounded-xl px-3 py-2.5 mb-4">
                <AlertCircle size={13}/> {error}
              </div>
            )}

            <button type="submit" disabled={loading || !email || !password}
              className="w-full py-3 bg-blue-600 hover:bg-blue-500 text-white text-sm font-bold rounded-xl transition-all shadow-lg shadow-blue-600/25 disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-2 mt-2">
              {loading ? <><RefreshCw size={14} className="animate-spin"/> Signing in…</> : <><LogOut size={14} className="rotate-180"/> Sign In</>}
            </button>
          </form>

          {/* Demo credentials */}
          <div className="mt-6 pt-5 border-t border-white/8">
            <p className="text-[10px] text-slate-500 text-center uppercase tracking-widest mb-3">Demo Credentials</p>
            <div className="space-y-1.5">
              {USER_DB.map(u => (
                <button key={u.id} type="button"
                  onClick={() => { setEmail(u.email); setPassword(u.password); setError(""); }}
                  className="w-full flex items-center justify-between px-3 py-2 rounded-xl bg-white/3 hover:bg-white/6 border border-white/8 hover:border-white/14 transition-all group">
                  <div className="flex items-center gap-2">
                    <Avatar initials={u.avatar} size="sm" color={u.role==="Admin"?"blue":u.role==="HRBP"?"amber":"purple"}/>
                    <div className="text-left">
                      <p className="text-xs font-semibold text-white">{u.name}</p>
                      <p className="text-[10px] text-slate-500">{u.email}</p>
                    </div>
                  </div>
                  <span className={`text-[10px] font-bold px-2 py-0.5 rounded-full
                    ${u.role==="Admin"?"bg-blue-500/15 text-blue-400":u.role==="HRBP"?"bg-amber-500/15 text-amber-400":"bg-purple-500/15 text-purple-400"}`}>
                    {u.role}
                  </span>
                </button>
              ))}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

// ─────────────────────────────────────────────
// MODULE A: INTERVIEW SCHEDULER
// ─────────────────────────────────────────────
const BLANK_IV = {
  candidate:"", email:"", role:"", level:"L1 - Screening",
  interviewerName:"", interviewerEmail:"",
  recruiterName:"", recruiterEmail:"",
  hrbpName:"", hrbpEmail:"",
  date:"", time:"", link:"", resumeName:null,
};

function InterviewScheduler() {
  const [interviews, setInterviews]     = useState(INITIAL_INTERVIEWS);
  const [filter, setFilter]             = useState("All");
  const [showModal, setShowModal]       = useState(false);
  const [editTarget, setEditTarget]     = useState(null);
  const [detailTarget, setDetailTarget] = useState(null);
  const [emailModal, setEmailModal]     = useState(null);
  const [emailBody, setEmailBody]       = useState("");
  const [feedbackModal, setFeedbackModal] = useState(null);
  const [feedbackForm, setFeedbackForm] = useState({ score:3, notes:"" });
  const [form, setForm]                 = useState(BLANK_IV);
  const resumeRef                       = useRef();

  function updateIv(id, patch) {
    setInterviews(p => p.map(iv => iv.id===id ? {...iv,...patch} : iv));
  }
  function openEdit(iv) {
    setForm({ candidate:iv.candidate, email:iv.email, role:iv.role, level:iv.level,
              interviewerName:iv.interviewerName, interviewerEmail:iv.interviewerEmail,
              recruiterName:iv.recruiterName, recruiterEmail:iv.recruiterEmail,
              hrbpName:iv.hrbpName, hrbpEmail:iv.hrbpEmail,
              date:iv.date, time:iv.time, link:iv.link, resumeName:iv.resumeName });
    setEditTarget(iv); setShowModal(true);
  }
  function handleSave() {
    if (editTarget) { updateIv(editTarget.id, {...form}); setEditTarget(null); }
    else setInterviews(p => [{ id:Date.now(), ...form, status:"Scheduled", rounds:[], inviteSent:false, rejectionEmailSent:false }, ...p]);
    setShowModal(false); setForm(BLANK_IV);
  }
  function handleDelete(id) {
    setInterviews(p => p.filter(iv => iv.id!==id));
    if (detailTarget?.id===id) setDetailTarget(null);
  }
  function openEmail(iv, type) {
    setEmailModal({ iv, type });
    setEmailBody(type==="invite"?buildInviteTemplate(iv):type==="panel"?buildPanelTemplate(iv):buildRejectionTemplate(iv));
  }
  function handleSendEmail() {
    const { iv, type } = emailModal;
    if (type==="invite"||type==="panel") updateIv(iv.id,{inviteSent:true});
    if (type==="rejection") updateIv(iv.id,{rejectionEmailSent:true,status:"Rejected"});
    setEmailModal(null);
  }
  function openFeedback(iv) { setFeedbackModal(iv); setFeedbackForm({score:3,notes:""}); }
  function handleFeedback() {
    const iv = feedbackModal;
    const r = { roundNum:iv.rounds.length+1, level:iv.level, interviewer:iv.interviewerName,
                score:feedbackForm.score, notes:feedbackForm.notes,
                date:new Date().toISOString().split("T")[0], sharedWithNext:false };
    updateIv(iv.id,{rounds:[...iv.rounds,r],status:"Completed"});
    if (detailTarget?.id===iv.id) setDetailTarget(p=>({...p,rounds:[...p.rounds,r],status:"Completed"}));
    setFeedbackModal(null);
  }
  function toggleShare(ivId, idx) {
    setInterviews(p => p.map(iv => {
      if (iv.id!==ivId) return iv;
      const rounds = iv.rounds.map((r,i) => i===idx?{...r,sharedWithNext:!r.sharedWithNext}:r);
      return {...iv,rounds};
    }));
    if (detailTarget?.id===ivId) setDetailTarget(p => ({...p,rounds:p.rounds.map((r,i)=>i===idx?{...r,sharedWithNext:!r.sharedWithNext}:r)}));
  }
  function handleRecruiterSelect(name) {
    const f = RECRUITERS_DB.find(r=>r.name===name);
    setForm(p => ({...p, recruiterName:name, recruiterEmail:f?.email||""}));
  }
  function handleHRBPSelect(name) {
    const f = HRBP_DB.find(h=>h.name===name);
    setForm(p => ({...p, hrbpName:name, hrbpEmail:f?.email||""}));
  }
  function handlePanelSelect(name) {
    const f = INTERVIEWERS_DB.find(i=>i.name===name);
    setForm(p => ({...p, interviewerName:name, interviewerEmail:f?.email||""}));
  }

  const filtered = filter==="All" ? interviews : interviews.filter(iv=>iv.status===filter);

  return (
    <div className="p-8">
      {/* Header — no "New Candidate" top-level button; only "Schedule Interview" which opens the form */}
      <div className="flex items-center justify-between mb-6 flex-wrap gap-3">
        <div>
          <h2 className="text-base font-bold text-white">Interview Scheduler</h2>
          <p className="text-xs text-slate-500 mt-0.5">{interviews.length} total records</p>
        </div>
        <div className="flex items-center gap-3 flex-wrap">
          <div className="flex bg-white/5 border border-white/8 rounded-xl p-1 gap-0.5">
            {STATUS_LIST.map(s => (
              <button key={s} onClick={() => setFilter(s)}
                className={`px-3 py-1.5 rounded-lg text-xs font-medium transition-all ${filter===s?"bg-blue-600 text-white shadow-sm":"text-slate-500 hover:text-slate-300"}`}>
                {s}
              </button>
            ))}
          </div>
          {/* Single entry point — "Schedule Interview" (no separate "New Candidate" tab) */}
          <button onClick={() => { setEditTarget(null); setForm(BLANK_IV); setShowModal(true); }}
            className="flex items-center gap-2 bg-blue-600 hover:bg-blue-500 text-white px-4 py-2.5 rounded-xl text-xs font-semibold transition-all shadow-lg shadow-blue-600/25">
            <Plus size={14}/> Schedule Interview
          </button>
        </div>
      </div>

      {/* Cards: Candidate Name · Role · Stage */}
      <div className="space-y-2">
        {filtered.map(iv => (
          <div key={iv.id} onClick={() => setDetailTarget(iv)}
            className="bg-white/3 border border-white/8 rounded-2xl px-5 py-4 hover:bg-white/5 hover:border-white/12 transition-all group cursor-pointer">
            <div className="flex items-center gap-4">
              <Avatar initials={initials(iv.candidate)} size="md" color="blue"/>
              <div className="flex-1 min-w-0">
                <div className="flex items-center gap-2 flex-wrap">
                  <p className="text-sm font-bold text-white">{iv.candidate}</p>
                  <span className="text-slate-600 text-xs">·</span>
                  <p className="text-xs text-slate-400">{iv.role}</p>
                  <span className="text-slate-600 text-xs">·</span>
                  <span className="text-[10px] font-semibold text-blue-400 bg-blue-500/10 border border-blue-500/20 px-2 py-0.5 rounded-full">{iv.level}</span>
                  <span className={`text-[10px] font-semibold px-2.5 py-0.5 rounded-full ${STATUS_COLORS[iv.status]}`}>{iv.status}</span>
                  {iv.resumeName && <span className="text-[10px] text-slate-500 flex items-center gap-1"><Paperclip size={10}/>{iv.resumeName}</span>}
                  {iv.inviteSent && <span className="text-[10px] text-emerald-400 flex items-center gap-1"><Send size={10}/> Invite Sent</span>}
                </div>
                <div className="flex items-center gap-3 mt-1 flex-wrap">
                  <span className="text-[10px] text-slate-500 flex items-center gap-1"><CalendarDays size={10}/>{formatDate(iv.date)} · {fmtTime(iv.time)}</span>
                  <span className="text-[10px] text-slate-500 flex items-center gap-1"><Users size={10}/>{iv.interviewerName} · <span className="text-slate-600">{iv.interviewerEmail}</span></span>
                  <span className="text-[10px] text-purple-400">Rec: {iv.recruiterName}</span>
                  <span className="text-[10px] text-amber-400">HRBP: {iv.hrbpName}</span>
                  {iv.rounds.length>0 && <span className="text-[10px] text-blue-400">{iv.rounds.length} round{iv.rounds.length>1?"s":""} logged</span>}
                </div>
              </div>
              {/* Action icons */}
              <div className="flex items-center gap-1.5 opacity-0 group-hover:opacity-100 transition-opacity" onClick={e=>e.stopPropagation()}>
                <button onClick={() => openEmail(iv,"invite")} title="Candidate Invite" className="p-1.5 rounded-lg bg-blue-500/10 text-blue-400 hover:bg-blue-500/20 transition-all"><Mail size={13}/></button>
                <button onClick={() => openEmail(iv,"panel")}  title="Panel Invite"     className="p-1.5 rounded-lg bg-purple-500/10 text-purple-400 hover:bg-purple-500/20 transition-all"><Send size={13}/></button>
                <button onClick={() => openFeedback(iv)}       title="Log Feedback"     className="p-1.5 rounded-lg bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 transition-all"><Edit3 size={13}/></button>
                {iv.status==="Rejected" && (
                  <button onClick={() => openEmail(iv,"rejection")} title="Send Rejection Email"
                    className={`p-1.5 rounded-lg transition-all ${iv.rejectionEmailSent?"bg-slate-500/10 text-slate-500":"bg-red-500/10 text-red-400 hover:bg-red-500/20"}`}>
                    <XCircle size={13}/>
                  </button>
                )}
                <button onClick={() => openEdit(iv)} className="p-1.5 rounded-lg bg-white/5 text-slate-400 hover:bg-white/10 transition-all"><RefreshCw size={13}/></button>
                <button onClick={() => handleDelete(iv.id)} className="p-1.5 rounded-lg bg-red-500/10 text-red-400 hover:bg-red-500/20 transition-all"><Trash2 size={13}/></button>
              </div>
            </div>
          </div>
        ))}
        {filtered.length===0 && (
          <div className="text-center py-16 text-slate-600"><Calendar size={32} className="mx-auto mb-2 opacity-30"/><p className="text-sm">No interviews in this view</p></div>
        )}
      </div>

      {/* ── SCHEDULE / EDIT MODAL ── */}
      {showModal && (
        <Modal wide title={editTarget?`Edit Interview · ${editTarget.candidate}`:"Schedule Interview"} onClose={()=>{setShowModal(false);setEditTarget(null);}}>

          <SectionDivider label="Candidate Details"/>
          <div className="grid grid-cols-2 gap-3">
            <Field label="Candidate Name">
              <input className={IC} placeholder="e.g. Priya Sharma" value={form.candidate} onChange={e=>setForm(p=>({...p,candidate:e.target.value}))}/>
            </Field>
            <Field label="Candidate Email">
              <input className={IC} type="email" placeholder="candidate@email.com" value={form.email} onChange={e=>setForm(p=>({...p,email:e.target.value}))}/>
            </Field>
            <Field label="Role Applied For">
              <input className={IC} placeholder="e.g. Senior Product Designer" value={form.role} onChange={e=>setForm(p=>({...p,role:e.target.value}))}/>
            </Field>
            <Field label="Interview Level / Stage">
              <select className={SC} value={form.level} onChange={e=>setForm(p=>({...p,level:e.target.value}))}>
                {INTERVIEW_LEVELS.map(l=><option key={l}>{l}</option>)}
              </select>
            </Field>
          </div>

          <SectionDivider label="Panel (Interviewer)"/>
          <div className="grid grid-cols-2 gap-3">
            <Field label="Interviewer Name">
              <select className={SC} value={form.interviewerName} onChange={e=>handlePanelSelect(e.target.value)}>
                <option value="">— Select Interviewer —</option>
                {INTERVIEWERS_DB.map(p=><option key={p.name}>{p.name}</option>)}
              </select>
            </Field>
            <Field label="Panel Email">
              <input className={IC} readOnly placeholder="Auto-filled" value={form.interviewerEmail}
                onChange={e=>setForm(p=>({...p,interviewerEmail:e.target.value}))}/>
            </Field>
          </div>

          <SectionDivider label="Other Manual Entry"/>
          <div className="grid grid-cols-2 gap-3">
            <Field label="Recruiter">
              <select className={SC} value={form.recruiterName} onChange={e=>handleRecruiterSelect(e.target.value)}>
                <option value="">— Select Recruiter —</option>
                {RECRUITERS_DB.map(r=><option key={r.name}>{r.name}</option>)}
              </select>
            </Field>
            <Field label="Recruiter Email">
              <input className={IC} placeholder="Auto-filled or enter manually" value={form.recruiterEmail}
                onChange={e=>setForm(p=>({...p,recruiterEmail:e.target.value}))}/>
            </Field>
            <Field label="HRBP">
              <select className={SC} value={form.hrbpName} onChange={e=>handleHRBPSelect(e.target.value)}>
                <option value="">— Select HRBP —</option>
                {HRBP_DB.map(h=><option key={h.name}>{h.name}</option>)}
              </select>
            </Field>
            <Field label="HRBP Email">
              <input className={IC} placeholder="Auto-filled or enter manually" value={form.hrbpEmail}
                onChange={e=>setForm(p=>({...p,hrbpEmail:e.target.value}))}/>
            </Field>
          </div>

          <SectionDivider label="Schedule Details"/>
          <div className="grid grid-cols-3 gap-3">
            <Field label="Date">
              <input className={IC} type="date" value={form.date} onChange={e=>setForm(p=>({...p,date:e.target.value}))}/>
            </Field>
            <Field label="Time">
              <input className={IC} type="time" value={form.time} onChange={e=>setForm(p=>({...p,time:e.target.value}))}/>
            </Field>
            <Field label="Meeting Link">
              <input className={IC} placeholder="https://zoom.us/j/..." value={form.link} onChange={e=>setForm(p=>({...p,link:e.target.value}))}/>
            </Field>
          </div>

          <SectionDivider label="Attachments"/>
          <Field label="Candidate Resume">
            <div className="flex items-center gap-3">
              <button type="button" onClick={() => resumeRef.current.click()}
                className="flex items-center gap-2 px-3 py-2 bg-white/5 border border-white/10 rounded-xl text-xs text-slate-400 hover:text-white hover:bg-white/8 transition-all">
                <Upload size={13}/> {form.resumeName?"Change":"Upload"} Resume
              </button>
              {form.resumeName && <span className="text-xs text-emerald-400 flex items-center gap-1"><Paperclip size={11}/>{form.resumeName}</span>}
            </div>
            <input ref={resumeRef} type="file" accept=".pdf,.doc,.docx" className="hidden"
              onChange={e=>{ const f=e.target.files[0]; if(f) setForm(p=>({...p,resumeName:f.name})); }}/>
          </Field>
          <Field label="Job Description (Auto-matched)">
            {JD_LIBRARY[form.role] ? (
              <div className="flex items-start gap-3 p-3 bg-emerald-500/8 border border-emerald-500/20 rounded-xl">
                <CheckCircle2 size={14} className="text-emerald-400 mt-0.5 flex-shrink-0"/>
                <div>
                  <p className="text-xs font-semibold text-emerald-300">{JD_LIBRARY[form.role].fileName}</p>
                  <p className="text-[10px] text-slate-500 mt-0.5">{JD_LIBRARY[form.role].content.slice(0,100)}…</p>
                </div>
              </div>
            ) : (
              <div className="flex items-center gap-2 p-3 bg-white/3 border border-white/8 rounded-xl text-xs text-slate-500">
                <FileText size={13}/> No JD found — enter exact role name to auto-match
              </div>
            )}
          </Field>

          <div className="flex gap-3 mt-4">
            <button onClick={()=>{setShowModal(false);setEditTarget(null);}}
              className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:text-white hover:bg-white/5 transition-all">Cancel</button>
            <button onClick={handleSave} disabled={!form.candidate||!form.role||!form.date}
              className="flex-1 py-2.5 rounded-xl bg-blue-600 hover:bg-blue-500 text-sm text-white font-semibold transition-all disabled:opacity-40">
              {editTarget?"Save Changes":"Schedule"}
            </button>
          </div>
        </Modal>
      )}

      {/* ── EMAIL MODAL ── */}
      {emailModal && (
        <Modal wide
          title={emailModal.type==="invite"?`Candidate Invite · ${emailModal.iv.candidate}`:emailModal.type==="panel"?`Panel Invite · ${emailModal.iv.interviewerName}`:`Rejection · ${emailModal.iv.candidate}`}
          onClose={()=>setEmailModal(null)}>
          <div className="mb-4 flex flex-wrap gap-2">
            {emailModal.iv.resumeName && <span className="flex items-center gap-1.5 text-[10px] bg-blue-500/10 border border-blue-500/20 text-blue-400 px-2.5 py-1.5 rounded-lg"><Paperclip size={10}/>{emailModal.iv.resumeName}</span>}
            {JD_LIBRARY[emailModal.iv.role] && <span className="flex items-center gap-1.5 text-[10px] bg-purple-500/10 border border-purple-500/20 text-purple-400 px-2.5 py-1.5 rounded-lg"><FileText size={10}/>{JD_LIBRARY[emailModal.iv.role].fileName}</span>}
          </div>
          <Field label="Email Body (Editable)">
            <textarea className={`${IC} h-80 resize-none font-mono text-xs leading-relaxed`} value={emailBody} onChange={e=>setEmailBody(e.target.value)}/>
          </Field>
          <div className="flex gap-3 mt-2">
            <button onClick={()=>setEmailModal(null)} className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:bg-white/5 transition-all">Cancel</button>
            <button onClick={handleSendEmail}
              className={`flex-1 py-2.5 rounded-xl text-sm text-white font-semibold transition-all flex items-center justify-center gap-2 ${emailModal.type==="rejection"?"bg-red-600 hover:bg-red-500":"bg-blue-600 hover:bg-blue-500"}`}>
              <Send size={14}/> Send Email
            </button>
          </div>
        </Modal>
      )}

      {/* ── FEEDBACK MODAL ── */}
      {feedbackModal && (
        <Modal title={`Log Feedback · ${feedbackModal.candidate}`} onClose={()=>setFeedbackModal(null)}>
          <div className="mb-4 p-3 bg-white/4 border border-white/8 rounded-xl">
            <p className="text-xs font-semibold text-white">{feedbackModal.candidate} — {feedbackModal.role}</p>
            <p className="text-[10px] text-slate-500 mt-0.5">Round {feedbackModal.rounds.length+1} · {feedbackModal.level}</p>
          </div>
          <Field label={`Score — Round ${feedbackModal.rounds.length+1}`}>
            <div className="flex gap-2">
              {[1,2,3,4,5].map(n=>(
                <button key={n} onClick={()=>setFeedbackForm(p=>({...p,score:n}))}
                  className={`w-10 h-10 rounded-xl border text-sm font-bold transition-all ${feedbackForm.score>=n?"bg-amber-500/20 border-amber-500/50 text-amber-300":"border-white/10 text-slate-600 hover:border-white/20"}`}>
                  {n}
                </button>
              ))}
            </div>
          </Field>
          <Field label="Notes">
            <textarea className={`${IC} resize-none h-28`} placeholder="Strengths, concerns, recommendation…"
              value={feedbackForm.notes} onChange={e=>setFeedbackForm(p=>({...p,notes:e.target.value}))}/>
          </Field>
          <div className="flex gap-3 mt-2">
            <button onClick={()=>setFeedbackModal(null)} className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:bg-white/5 transition-all">Cancel</button>
            <button onClick={handleFeedback} className="flex-1 py-2.5 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-sm text-white font-semibold transition-all">Save Feedback</button>
          </div>
        </Modal>
      )}

      {/* ── DETAIL PANEL ── */}
      {detailTarget && (
        <Modal wide title={`Interview Detail · ${detailTarget.candidate}`} onClose={()=>setDetailTarget(null)}>
          <div className="flex items-center gap-3 mb-5 p-3 bg-white/4 rounded-xl border border-white/8">
            <Avatar initials={initials(detailTarget.candidate)} size="lg" color="blue"/>
            <div className="flex-1 min-w-0">
              <p className="text-sm font-bold text-white">{detailTarget.candidate}</p>
              <p className="text-xs text-slate-400">{detailTarget.role} · {detailTarget.level}</p>
              <div className="flex items-center gap-3 mt-1">
                <span className="text-[10px] text-purple-400">Rec: {detailTarget.recruiterName}</span>
                <span className="text-[10px] text-amber-400">HRBP: {detailTarget.hrbpName}</span>
              </div>
            </div>
            <span className={`text-[10px] font-semibold px-3 py-1 rounded-full ${STATUS_COLORS[detailTarget.status]}`}>{detailTarget.status}</span>
          </div>
          {detailTarget.rounds.length===0 ? (
            <p className="text-xs text-slate-500 text-center py-8">No feedback logged yet.</p>
          ) : (
            <div className="space-y-3">
              <p className="text-[10px] font-semibold text-slate-500 uppercase tracking-widest mb-2">Interview Rounds</p>
              {detailTarget.rounds.map((r,i) => (
                <div key={i} className={`p-4 rounded-xl border ${r.sharedWithNext?"bg-blue-500/8 border-blue-500/20":"bg-white/3 border-white/8"}`}>
                  <div className="flex items-start justify-between mb-2">
                    <div>
                      <p className="text-xs font-bold text-white">Round {r.roundNum} — {r.level}</p>
                      <p className="text-[10px] text-slate-500">{r.interviewer} · {formatDate(r.date)}</p>
                    </div>
                    <div className="flex items-center gap-2">
                      <div className="flex gap-0.5">
                        {Array.from({length:5},(_,si)=><Star key={si} size={11} className={si<r.score?"text-amber-400 fill-amber-400":"text-slate-700"}/>)}
                      </div>
                      <button onClick={()=>toggleShare(detailTarget.id,i)}
                        className={`flex items-center gap-1 text-[10px] px-2 py-1 rounded-lg border transition-all ${r.sharedWithNext?"bg-blue-500/15 border-blue-500/25 text-blue-400":"bg-white/5 border-white/10 text-slate-500 hover:text-slate-300"}`}>
                        {r.sharedWithNext?<Unlock size={10}/>:<Lock size={10}/>}
                        {r.sharedWithNext?"Shared":"Share"}
                      </button>
                    </div>
                  </div>
                  <p className="text-xs text-slate-400 leading-relaxed">{r.notes}</p>
                </div>
              ))}
            </div>
          )}
        </Modal>
      )}
    </div>
  );
}

// ─────────────────────────────────────────────
// MODULE B: POST-OFFER ENGAGEMENT
// ─────────────────────────────────────────────
const PIPELINE_STAGES = ["Offer Extended","Document Collection","Background Check","Ready for Day 1"];

function PreboardingModule() {
  const [candidates, setCandidates] = useState(MOCK_CANDIDATES);
  const [selected, setSelected]     = useState(MOCK_CANDIDATES[0]);
  const [touchForm, setTouchForm]   = useState({ type:"Call", note:"" });
  const [showTouchModal, setShowTouchModal] = useState(false);

  function toggleTask(candId, idx) {
    setCandidates(prev => {
      const updated = prev.map(c => {
        if (c.id!==candId) return c;
        const tasks = c.tasks.map((t,i) => i!==idx?t:{...t,status:{Pending:"Submitted",Submitted:"Verified",Verified:"Pending"}[t.status]});
        const prog = Math.round(tasks.filter(t=>t.status==="Verified").length/tasks.length*100);
        return {...c,tasks,progress:prog};
      });
      setSelected(updated.find(c=>c.id===candId));
      return updated;
    });
  }

  function addTouchpoint() {
    const tp = { date:new Date().toISOString().split("T")[0], ...touchForm };
    setCandidates(prev => {
      const updated = prev.map(c => c.id===selected.id?{...c,touchpoints:[...c.touchpoints,tp]}:c);
      setSelected(updated.find(c=>c.id===selected.id));
      return updated;
    });
    setTouchForm({type:"Call",note:""}); setShowTouchModal(false);
  }

  return (
    <div className="p-8 flex gap-6 h-full">
      {/* Left */}
      <div className="w-72 flex-shrink-0 space-y-2">
        <div className="flex items-center justify-between mb-4">
          <h2 className="text-sm font-bold text-white">Pre-boarding Pipeline</h2>
          <span className="text-xs text-slate-500">{candidates.length} candidates</span>
        </div>
        <div className="flex flex-col gap-1 mb-4">
          {PIPELINE_STAGES.map((s,i)=>{
            const dotCl = i===3?"bg-emerald-400":i===2?"bg-blue-400":i===1?"bg-amber-400":"bg-slate-500";
            return <div key={s} className="flex items-center gap-2 text-xs text-slate-500">
              <div className={`w-2 h-2 rounded-full flex-shrink-0 ${dotCl}`}/><span className="flex-1">{s}</span>
              <span className="text-slate-600 font-mono">{candidates.filter(c=>c.stage===s).length}</span>
            </div>;
          })}
        </div>
        {candidates.map(c=>(
          <button key={c.id} onClick={()=>setSelected(c)}
            className={`w-full text-left rounded-2xl p-4 border transition-all ${selected?.id===c.id?"bg-blue-500/12 border-blue-500/30":"bg-white/3 border-white/8 hover:bg-white/5"}`}>
            <div className="flex items-center gap-3 mb-2.5">
              <Avatar initials={c.avatar} size="md" color={selected?.id===c.id?"blue":"purple"}/>
              <div className="min-w-0">
                <p className="text-sm font-semibold text-white truncate">{c.name}</p>
                <p className="text-xs text-slate-500 truncate">{c.role}</p>
                <div className="flex items-center gap-2 mt-0.5">
                  <span className="text-[10px] text-purple-400 truncate">Rec: {c.recruiterName}</span>
                </div>
              </div>
            </div>
            <div>
              <div className="flex justify-between text-[10px] mb-1"><span className="text-slate-500">{c.stage}</span><span className="text-slate-400 font-semibold">{c.progress}%</span></div>
              <div className="h-1.5 bg-white/8 rounded-full overflow-hidden">
                <div className="h-full rounded-full transition-all duration-700" style={{width:`${c.progress}%`,background:c.progress>=90?"#34d399":c.progress>=60?"#60a5fa":"#f59e0b"}}/>
              </div>
            </div>
            <p className="text-[10px] text-slate-600 mt-1.5">Day 1: {formatDate(c.joiningDate)}</p>
          </button>
        ))}
      </div>

      {/* Right */}
      {selected && (
        <div className="flex-1 min-w-0">
          <div className="flex items-start justify-between mb-6 flex-wrap gap-3">
            <div className="flex items-center gap-4">
              <Avatar initials={selected.avatar} size="lg" color="blue"/>
              <div>
                <h2 className="text-base font-bold text-white">{selected.name}</h2>
                <p className="text-xs text-slate-400">{selected.role}</p>
                <div className="flex items-center gap-3 mt-1.5 flex-wrap">
                  <span className="text-[10px] text-slate-500">Offer: {formatDate(selected.offerDate)}</span>
                  <span className="text-[10px] text-emerald-400 font-semibold">Day 1: {formatDate(selected.joiningDate)}</span>
                  <span className="text-[10px] text-purple-400">Rec: {selected.recruiterName}</span>
                  <span className="text-[10px] text-amber-400">HRBP: {selected.hrbpName}</span>
                </div>
              </div>
            </div>
            <div className="flex items-center gap-1">
              {PIPELINE_STAGES.map((s,i)=>(
                <div key={s} className="flex items-center gap-1">
                  <div className={`w-2 h-2 rounded-full transition-all ${PIPELINE_STAGES.indexOf(selected.stage)>=i?"bg-blue-400 shadow-sm shadow-blue-400/50":"bg-white/10"}`}/>
                  {i<PIPELINE_STAGES.length-1 && <div className={`w-5 h-px ${PIPELINE_STAGES.indexOf(selected.stage)>i?"bg-blue-400/50":"bg-white/8"}`}/>}
                </div>
              ))}
            </div>
          </div>
          <div className="grid grid-cols-2 gap-5">
            <div className="bg-white/3 border border-white/8 rounded-2xl p-5">
              <h3 className="text-xs font-bold text-white uppercase tracking-wider mb-4 flex items-center gap-2"><FileText size={13} className="text-blue-400"/> Document Checklist</h3>
              <div className="space-y-2.5">
                {selected.tasks.map((task,i)=>(
                  <button key={i} onClick={()=>toggleTask(selected.id,i)} className="w-full flex items-center gap-3 p-2.5 rounded-xl hover:bg-white/5 transition-all">
                    <TaskIcon status={task.status}/>
                    <span className={`text-xs flex-1 text-left ${task.status==="Pending"?"text-slate-500":"text-slate-300"}`}>{task.label}</span>
                    <span className={`text-[10px] font-semibold ${TASK_CLR[task.status]}`}>{task.status}</span>
                  </button>
                ))}
              </div>
              <p className="text-[10px] text-slate-600 mt-3 text-center">Click to cycle status</p>
            </div>
            <div className="bg-white/3 border border-white/8 rounded-2xl p-5">
              <div className="flex items-center justify-between mb-4">
                <h3 className="text-xs font-bold text-white uppercase tracking-wider flex items-center gap-2"><Activity size={13} className="text-emerald-400"/> Engagement Log</h3>
                <button onClick={()=>setShowTouchModal(true)} className="flex items-center gap-1 text-[10px] text-blue-400 bg-blue-500/10 px-2 py-1 rounded-lg border border-blue-500/20"><Plus size={10}/>Add</button>
              </div>
              <div className="space-y-2.5 max-h-52 overflow-y-auto pr-1">
                {[...selected.touchpoints].reverse().map((tp,i)=>(
                  <div key={i} className="flex gap-3 p-2.5 bg-white/3 rounded-xl">
                    <div className={`w-6 h-6 rounded-full flex-shrink-0 flex items-center justify-center ${tp.type==="Call"?"bg-blue-500/20":"bg-purple-500/20"}`}>
                      {tp.type==="Call"?<Phone size={10} className="text-blue-400"/>:<Mail size={10} className="text-purple-400"/>}
                    </div>
                    <div className="min-w-0">
                      <div className="flex items-center gap-2"><span className="text-[10px] font-semibold text-slate-400">{tp.type}</span><span className="text-[10px] text-slate-600">{formatDate(tp.date)}</span></div>
                      <p className="text-xs text-slate-400 mt-0.5 leading-relaxed">{tp.note}</p>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          </div>
        </div>
      )}
      {showTouchModal && (
        <Modal title="Add Engagement Touchpoint" onClose={()=>setShowTouchModal(false)}>
          <Field label="Type"><select className={SC} value={touchForm.type} onChange={e=>setTouchForm(p=>({...p,type:e.target.value}))}><option>Call</option><option>Email</option><option>Message</option></select></Field>
          <Field label="Note"><textarea className={`${IC} h-28 resize-none`} placeholder="What was discussed?" value={touchForm.note} onChange={e=>setTouchForm(p=>({...p,note:e.target.value}))}/></Field>
          <div className="flex gap-3 mt-2">
            <button onClick={()=>setShowTouchModal(false)} className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:bg-white/5 transition-all">Cancel</button>
            <button onClick={addTouchpoint} disabled={!touchForm.note} className="flex-1 py-2.5 rounded-xl bg-blue-600 hover:bg-blue-500 text-sm text-white font-semibold transition-all disabled:opacity-40">Log Touchpoint</button>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─────────────────────────────────────────────
// MODULE C: PROBATION TRACKER
// ─────────────────────────────────────────────
const PROB_STATUS_COLORS = {
  "Under Probation":          "bg-blue-500/15 text-blue-400 border border-blue-500/20",
  "Due for Review":           "bg-amber-500/15 text-amber-400 border border-amber-500/20",
  "Pending HRBP Confirmation":"bg-purple-500/15 text-purple-400 border border-purple-500/20",
  "Completed":                "bg-emerald-500/15 text-emerald-400 border border-emerald-500/20",
  "Extended":                 "bg-red-500/15 text-red-400 border border-red-500/20",
};

function AddEmployeeModal({ onClose, onAdd }) {
  const [form, setForm] = useState({
    empId:"", name:"", dept:"", location:"", source:"LinkedIn",
    doj:"", manager:"",
    recruiterName:"", recruiterEmail:"",
    hrbpName:"", hrbpEmail:"",
    referrerName:"", referrerEmail:"",
  });
  function handleRecruiter(name) {
    const f = RECRUITERS_DB.find(r=>r.name===name);
    setForm(p=>({...p,recruiterName:name,recruiterEmail:f?.email||""}));
  }
  function handleHRBP(name) {
    const f = HRBP_DB.find(h=>h.name===name);
    setForm(p=>({...p,hrbpName:name,hrbpEmail:f?.email||""}));
  }
  function handleAdd() {
    const doj = form.doj;
    const probEnd = daysOffset(doj, 180);
    onAdd({ id:`C${Date.now()}`, empId:form.empId||`EMP${Date.now()}`, name:form.name, dept:form.dept, location:form.location, source:form.source,
      doj, probationEnd:probEnd, reviewStatus:"Under Probation", manager:form.manager,
      recruiterName:form.recruiterName, recruiterEmail:form.recruiterEmail,
      hrbpName:form.hrbpName, hrbpEmail:form.hrbpEmail,
      referrerName:form.referrerName, referrerEmail:form.referrerEmail,
      notifStatus:"Not Triggered", reviewLog:null });
    onClose();
  }
  return (
    <Modal wide title="Add New Joiner — Probation Record" onClose={onClose}>
      <SectionDivider label="Employee Details"/>
      <div className="grid grid-cols-2 gap-3">
        <Field label="Employee ID (optional)"><input className={IC} placeholder="e.g. EMP007" value={form.empId} onChange={e=>setForm(p=>({...p,empId:e.target.value}))}/></Field>
        <Field label="Full Name *"><input className={IC} placeholder="Candidate full name" value={form.name} onChange={e=>setForm(p=>({...p,name:e.target.value}))}/></Field>
        <Field label="Department *">
          <select className={SC} value={form.dept} onChange={e=>setForm(p=>({...p,dept:e.target.value}))}>
            <option value="">— Select Department —</option>
            {DEPARTMENTS.map(d=><option key={d}>{d}</option>)}
          </select>
        </Field>
        <Field label="Location">
          <select className={SC} value={form.location} onChange={e=>setForm(p=>({...p,location:e.target.value}))}>
            {LOCATIONS.map(l=><option key={l}>{l}</option>)}
          </select>
        </Field>
        <Field label="Candidate Source">
          <select className={SC} value={form.source} onChange={e=>setForm(p=>({...p,source:e.target.value}))}>
            {SOURCES.map(s=><option key={s}>{s}</option>)}
          </select>
        </Field>
        <Field label="Date of Joining *"><input className={IC} type="date" value={form.doj} onChange={e=>setForm(p=>({...p,doj:e.target.value}))}/></Field>
        <Field label="Reporting Manager"><input className={IC} placeholder="Manager name" value={form.manager} onChange={e=>setForm(p=>({...p,manager:e.target.value}))}/></Field>
      </div>

      <SectionDivider label="Other Manual Entry"/>
      <div className="grid grid-cols-2 gap-3">
        <Field label="Recruiter">
          <select className={SC} value={form.recruiterName} onChange={e=>handleRecruiter(e.target.value)}>
            <option value="">— Select Recruiter —</option>
            {RECRUITERS_DB.map(r=><option key={r.name}>{r.name}</option>)}
          </select>
        </Field>
        <Field label="Recruiter Email"><input className={IC} placeholder="Auto-filled" value={form.recruiterEmail} onChange={e=>setForm(p=>({...p,recruiterEmail:e.target.value}))}/></Field>
        <Field label="HRBP">
          <select className={SC} value={form.hrbpName} onChange={e=>handleHRBP(e.target.value)}>
            <option value="">— Select HRBP —</option>
            {HRBP_DB.map(h=><option key={h.name}>{h.name}</option>)}
          </select>
        </Field>
        <Field label="HRBP Email"><input className={IC} placeholder="Auto-filled" value={form.hrbpEmail} onChange={e=>setForm(p=>({...p,hrbpEmail:e.target.value}))}/></Field>
        <Field label="Referrer Name (if Referral)"><input className={IC} placeholder="Referrer full name" value={form.referrerName} onChange={e=>setForm(p=>({...p,referrerName:e.target.value}))}/></Field>
        <Field label="Referrer Email"><input className={IC} placeholder="referrer@company.com" value={form.referrerEmail} onChange={e=>setForm(p=>({...p,referrerEmail:e.target.value}))}/></Field>
      </div>

      {form.doj && (
        <div className="mt-2 p-3 bg-blue-500/8 border border-blue-500/20 rounded-xl text-xs text-blue-300 flex items-center gap-2">
          <CalendarDays size={13}/> Probation end date will be auto-set to: <strong>{formatDate(daysOffset(form.doj,180))}</strong>
        </div>
      )}

      <div className="flex gap-3 mt-4">
        <button onClick={onClose} className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:bg-white/5 transition-all">Cancel</button>
        <button onClick={handleAdd} disabled={!form.name||!form.dept||!form.doj}
          className="flex-1 py-2.5 rounded-xl bg-blue-600 hover:bg-blue-500 text-sm text-white font-semibold transition-all disabled:opacity-40">Add Employee</button>
      </div>
    </Modal>
  );
}

function ProbationTracker() {
  const [employees, setEmployees]     = useState(MOCK_EMPLOYEES);
  const [reviewModal, setReviewModal] = useState(null);
  const [reviewForm, setReviewForm]   = useState({ type:"30-Day", notes:"", decision:"Confirm Employment" });
  const [sortBy, setSortBy]           = useState("daysLeft");
  const [showAddModal, setShowAddModal] = useState(false);
  const [filterStatus, setFilterStatus] = useState("All");

  function getUrgency(dateStr, status) {
    if (["Completed","Extended"].includes(status)) return "ok";
    const d = daysUntil(dateStr);
    if (d<0) return "overdue";
    if (d<=14) return "critical";
    if (d<=30) return "warning";
    return "ok";
  }
  const urgCfg = {
    overdue:  { row:"bg-red-500/8 border-l-2 border-l-red-500",   badge:"bg-red-500/15 text-red-400 border border-red-500/25" },
    critical: { row:"bg-red-500/5 border-l-2 border-l-red-400",   badge:"bg-red-500/10 text-red-400 border border-red-400/20" },
    warning:  { row:"bg-amber-500/5 border-l-2 border-l-amber-500", badge:"bg-amber-500/10 text-amber-400 border border-amber-400/20" },
    ok:       { row:"bg-white/2 border-l-2 border-l-transparent",  badge:"bg-white/8 text-slate-400 border border-white/10" },
  };

  const sorted = [...employees]
    .filter(e => filterStatus==="All" || e.reviewStatus===filterStatus)
    .sort((a,b)=>{
      if (sortBy==="daysLeft") return daysUntil(a.probationEnd)-daysUntil(b.probationEnd);
      if (sortBy==="dept") return a.dept.localeCompare(b.dept);
      return a.name.localeCompare(b.name);
    });

  function handleReview() {
    setEmployees(p=>p.map(e=>e.id===reviewModal.id?{...e,reviewStatus:reviewForm.decision==="Confirm Employment"?"Completed":reviewForm.decision==="Extend Probation"?"Extended":"Completed",reviewLog:reviewForm}:e));
    setReviewModal(null);
  }

  const statusOptions = ["All","Under Probation","Due for Review","Pending HRBP Confirmation","Completed","Extended"];

  return (
    <div className="p-8">
      {/* Stats */}
      <div className="grid grid-cols-5 gap-3 mb-6">
        <StatCard label="Total"            value={employees.length}                                                                              icon={Users}       color="blue"/>
        <StatCard label="Under Probation"  value={employees.filter(e=>e.reviewStatus==="Under Probation").length}                               icon={Clock}       color="blue" sub="Active"/>
        <StatCard label="Due / Pending"    value={employees.filter(e=>["Due for Review","Pending HRBP Confirmation"].includes(e.reviewStatus)).length} icon={AlertCircle} color="amber" sub="Needs action"/>
        <StatCard label="Completed"        value={employees.filter(e=>e.reviewStatus==="Completed").length}                                     icon={CheckCircle2} color="emerald"/>
        <StatCard label="Extended"         value={employees.filter(e=>e.reviewStatus==="Extended").length}                                      icon={AlertTriangle} color="red"/>
      </div>

      {/* Controls */}
      <div className="flex items-center justify-between mb-3 flex-wrap gap-3">
        <div className="flex items-center gap-2 flex-wrap">
          <h2 className="text-sm font-bold text-white">Probation Register</h2>
          <div className="flex items-center gap-1">
            {["All",...["Under Probation","Due for Review","Pending HRBP Confirmation","Completed","Extended"]].map(s=>(
              s==="All"?null:null // handled below
            ))}
          </div>
          {/* Status filter pills */}
          <select className="bg-white/5 border border-white/8 text-xs text-slate-300 rounded-lg px-2 py-1.5 focus:outline-none focus:border-blue-500/40"
            value={filterStatus} onChange={e=>setFilterStatus(e.target.value)}>
            {statusOptions.map(s=><option key={s} value={s} style={{background:"#0f1623"}}>{s}</option>)}
          </select>
        </div>
        <div className="flex items-center gap-2">
          <span className="text-xs text-slate-500">Sort:</span>
          {["daysLeft","dept","name"].map(s=>(
            <button key={s} onClick={()=>setSortBy(s)}
              className={`text-xs px-3 py-1.5 rounded-lg transition-all ${sortBy===s?"bg-blue-600 text-white":"text-slate-500 hover:text-slate-300 bg-white/5 border border-white/8"}`}>
              {s==="daysLeft"?"Days Left":s==="dept"?"Dept":"Name"}
            </button>
          ))}
          {/* Add New Joiner within Probation module */}
          <button onClick={()=>setShowAddModal(true)}
            className="flex items-center gap-1.5 bg-blue-600 hover:bg-blue-500 text-white px-3 py-1.5 rounded-xl text-xs font-semibold transition-all shadow-lg shadow-blue-600/20">
            <Plus size={12}/> Add New Joiner
          </button>
        </div>
      </div>

      {/* Table */}
      <div className="bg-white/2 border border-white/8 rounded-2xl overflow-hidden">
        <div className="grid grid-cols-8 gap-2 px-5 py-3 border-b border-white/6 bg-white/3">
          {["Employee","Dept","DoJ","Probation End","Days Left","Review Status","Recruiter / HRBP","Action"].map(h=>(
            <p key={h} className="text-[10px] font-semibold text-slate-500 uppercase tracking-wider">{h}</p>
          ))}
        </div>
        <div className="divide-y divide-white/5">
          {sorted.map(emp=>{
            const urg = getUrgency(emp.probationEnd,emp.reviewStatus);
            const cfg = urgCfg[urg];
            const d   = daysUntil(emp.probationEnd);
            const UrgIcon = urg==="overdue"||urg==="critical"?AlertCircle:urg==="warning"?AlertTriangle:CheckCircle2;
            const icl = urg==="ok"?"text-emerald-400":urg==="warning"?"text-amber-400":"text-red-400";
            return (
              <div key={emp.id} className={`grid grid-cols-8 gap-2 px-5 py-3.5 items-center transition-all hover:bg-white/3 ${cfg.row}`}>
                <div className="flex items-center gap-2">
                  <Avatar initials={initials(emp.name)} size="sm" color="blue"/>
                  <div><p className="text-xs font-semibold text-white leading-tight">{emp.name}</p><p className="text-[10px] text-slate-600">{emp.empId}</p></div>
                </div>
                <span className="text-[10px] bg-white/6 border border-white/8 text-slate-400 px-2 py-0.5 rounded-lg truncate">{emp.dept}</span>
                <p className="text-[10px] text-slate-400">{formatDate(emp.doj)}</p>
                <p className="text-[10px] text-slate-300 font-medium">{formatDate(emp.probationEnd)}</p>
                <div className="flex items-center gap-1.5">
                  <UrgIcon size={11} className={icl}/>
                  <span className={`text-[10px] font-semibold px-2 py-0.5 rounded-full ${cfg.badge}`}>{d<0?`${Math.abs(d)}d ago`:`${d}d`}</span>
                </div>
                <span className={`text-[10px] font-semibold px-2 py-0.5 rounded-full w-fit ${PROB_STATUS_COLORS[emp.reviewStatus]||"bg-slate-500/15 text-slate-400"}`}>
                  {emp.reviewStatus}
                </span>
                <div>
                  <p className="text-[10px] text-purple-400 truncate">{emp.recruiterName}</p>
                  <p className="text-[10px] text-amber-400 truncate">{emp.hrbpName}</p>
                </div>
                <div>
                  {!["Completed"].includes(emp.reviewStatus) ? (
                    <button onClick={()=>{setReviewModal(emp);setReviewForm({type:"30-Day",notes:"",decision:"Confirm Employment"});}}
                      className="flex items-center gap-1 text-[10px] bg-blue-500/15 border border-blue-500/25 text-blue-400 hover:bg-blue-500/25 px-2.5 py-1.5 rounded-lg transition-all font-semibold whitespace-nowrap">
                      <Edit3 size={9}/> Log Review
                    </button>
                  ) : (
                    <div>
                      <p className="text-[10px] text-emerald-400 font-semibold">{emp.reviewLog?.decision}</p>
                      <p className="text-[10px] text-slate-600">{emp.reviewLog?.type}</p>
                    </div>
                  )}
                </div>
              </div>
            );
          })}
          {sorted.length===0 && (
            <div className="text-center py-10 text-slate-600 col-span-8">
              <Users size={28} className="mx-auto mb-2 opacity-30"/><p className="text-sm">No records in this view</p>
            </div>
          )}
        </div>
      </div>

      {/* Add Modal */}
      {showAddModal && <AddEmployeeModal onClose={()=>setShowAddModal(false)} onAdd={e=>setEmployees(p=>[e,...p])}/>}

      {/* Review Modal */}
      {reviewModal && (
        <Modal title={`Log Review · ${reviewModal.name}`} onClose={()=>setReviewModal(null)}>
          <div className="flex items-center gap-3 mb-5 p-3 bg-white/4 rounded-xl border border-white/8">
            <Avatar initials={initials(reviewModal.name)} size="md" color="blue"/>
            <div>
              <p className="text-sm font-semibold text-white">{reviewModal.name}</p>
              <p className="text-xs text-slate-500">{reviewModal.dept} · {reviewModal.manager}</p>
              <div className="flex gap-3 mt-1">
                <span className="text-[10px] text-purple-400">Rec: {reviewModal.recruiterName}</span>
                <span className="text-[10px] text-amber-400">HRBP: {reviewModal.hrbpName}</span>
              </div>
            </div>
          </div>
          <Field label="Review Type">
            <select className={SC} value={reviewForm.type} onChange={e=>setReviewForm(p=>({...p,type:e.target.value}))}>
              <option>30-Day</option><option>60-Day</option><option>90-Day</option><option>180-Day Final</option>
            </select>
          </Field>
          <Field label="Performance Notes">
            <textarea className={`${IC} h-28 resize-none`} placeholder="Manager observations and performance notes…"
              value={reviewForm.notes} onChange={e=>setReviewForm(p=>({...p,notes:e.target.value}))}/>
          </Field>
          <Field label="Decision">
            <select className={SC} value={reviewForm.decision} onChange={e=>setReviewForm(p=>({...p,decision:e.target.value}))}>
              <option>Confirm Employment</option><option>Extend Probation</option><option>Terminate</option>
            </select>
          </Field>
          {reviewForm.decision==="Terminate" && (
            <div className="flex items-start gap-2 p-3 bg-red-500/10 border border-red-500/25 rounded-xl mb-4 text-xs text-red-300">
              <AlertTriangle size={13} className="flex-shrink-0 mt-0.5"/> This action is irreversible. Ensure proper documentation.
            </div>
          )}
          <div className="flex gap-3 mt-2">
            <button onClick={()=>setReviewModal(null)} className="flex-1 py-2.5 rounded-xl border border-white/10 text-sm text-slate-400 hover:bg-white/5 transition-all">Cancel</button>
            <button onClick={handleReview}
              className={`flex-1 py-2.5 rounded-xl text-sm text-white font-semibold transition-all ${reviewForm.decision==="Terminate"?"bg-red-600 hover:bg-red-500":"bg-blue-600 hover:bg-blue-500"}`}>
              Submit Review
            </button>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─────────────────────────────────────────────
// OVERVIEW DASHBOARD
// ─────────────────────────────────────────────
function Overview({ setActive }) {
  const upcomingIvs   = INITIAL_INTERVIEWS.filter(i=>i.status==="Scheduled");
  const criticalProb  = MOCK_EMPLOYEES.filter(e=>{ const d=daysUntil(e.probationEnd); return d>=0&&d<=14&&!["Completed","Extended"].includes(e.reviewStatus); });

  return (
    <div className="p-8 space-y-6">
      <div className="bg-gradient-to-r from-blue-600/20 to-blue-800/10 border border-blue-500/20 rounded-2xl p-6">
        <div className="flex items-start justify-between flex-wrap gap-3">
          <div>
            <p className="text-xs text-blue-400 font-semibold uppercase tracking-widest mb-1">Good Morning</p>
            <h2 className="text-xl font-bold text-white tracking-tight">Welcome back, Admin</h2>
            <p className="text-sm text-slate-400 mt-1">Thursday, 21 May 2026 · HR Administration Dashboard</p>
          </div>
          <div className="flex items-center gap-2 bg-blue-500/15 border border-blue-500/25 px-4 py-2.5 rounded-xl">
            <Shield size={14} className="text-blue-400"/><span className="text-xs text-blue-300 font-semibold">HR Admin</span>
          </div>
        </div>
      </div>
      <div className="grid grid-cols-4 gap-4">
        <StatCard label="Scheduled Interviews" value={upcomingIvs.length}                                                                   icon={Calendar}     color="blue"   sub="This week"/>
        <StatCard label="In Pre-boarding"       value={MOCK_CANDIDATES.length}                                                               icon={UserCheck}    color="emerald" sub="Active candidates"/>
        <StatCard label="On Probation"          value={MOCK_EMPLOYEES.filter(e=>!["Completed"].includes(e.reviewStatus)).length}             icon={ClipboardList} color="amber"/>
        <StatCard label="Urgent Reviews"        value={criticalProb.length}                                                                  icon={AlertCircle}  color="red"    sub="≤14 days left"/>
      </div>
      <div className="grid grid-cols-2 gap-5">
        <div className="bg-white/3 border border-white/8 rounded-2xl p-5">
          <div className="flex items-center justify-between mb-4">
            <h3 className="text-sm font-bold text-white flex items-center gap-2"><Calendar size={14} className="text-blue-400"/> Upcoming Interviews</h3>
            <button onClick={()=>setActive("interviews")} className="text-[10px] text-blue-400 hover:text-blue-300 flex items-center gap-1">View all<ChevronRight size={10}/></button>
          </div>
          <div className="space-y-2">
            {upcomingIvs.slice(0,4).map(iv=>(
              <div key={iv.id} className="flex items-center gap-3 p-2.5 bg-white/3 rounded-xl">
                <Avatar initials={initials(iv.candidate)} size="sm" color="blue"/>
                <div className="flex-1 min-w-0">
                  <p className="text-xs font-semibold text-white truncate">{iv.candidate}</p>
                  <p className="text-[10px] text-slate-500 truncate">{iv.role} · {iv.level}</p>
                </div>
                <div className="text-right flex-shrink-0">
                  <p className="text-[10px] text-slate-400">{formatDate(iv.date)}</p>
                  <p className="text-[10px] text-slate-600">{fmtTime(iv.time)}</p>
                </div>
              </div>
            ))}
          </div>
        </div>
        <div className="bg-white/3 border border-white/8 rounded-2xl p-5">
          <div className="flex items-center justify-between mb-4">
            <h3 className="text-sm font-bold text-white flex items-center gap-2"><AlertCircle size={14} className="text-red-400"/> Urgent Probation Reviews</h3>
            <button onClick={()=>setActive("probation")} className="text-[10px] text-blue-400 hover:text-blue-300 flex items-center gap-1">View all<ChevronRight size={10}/></button>
          </div>
          <div className="space-y-2">
            {MOCK_EMPLOYEES.filter(e=>{ const d=daysUntil(e.probationEnd); return d>=0&&d<=30&&!["Completed","Extended"].includes(e.reviewStatus); }).slice(0,5).map(e=>{
              const d=daysUntil(e.probationEnd);
              return (
                <div key={e.id} className={`flex items-center gap-3 p-2.5 rounded-xl ${d<=14?"bg-red-500/8 border border-red-500/15":"bg-amber-500/8 border border-amber-500/15"}`}>
                  <Avatar initials={initials(e.name)} size="sm" color={d<=14?"red":"amber"}/>
                  <div className="flex-1 min-w-0"><p className="text-xs font-semibold text-white">{e.name}</p><p className="text-[10px] text-slate-500">{e.dept} · HRBP: {e.hrbpName}</p></div>
                  <span className={`text-[10px] font-bold px-2 py-0.5 rounded-full flex-shrink-0 ${d<=14?"text-red-400 bg-red-500/15":"text-amber-400 bg-amber-500/15"}`}>{d}d</span>
                </div>
              );
            })}
            {MOCK_EMPLOYEES.filter(e=>{ const d=daysUntil(e.probationEnd); return d>=0&&d<=30&&!["Completed","Extended"].includes(e.reviewStatus); }).length===0 && (
              <p className="text-xs text-slate-500 text-center py-6">All clear — no urgent reviews!</p>
            )}
          </div>
        </div>
      </div>
      <div className="bg-white/3 border border-white/8 rounded-2xl p-5">
        <div className="flex items-center justify-between mb-4">
          <h3 className="text-sm font-bold text-white flex items-center gap-2"><UserCheck size={14} className="text-emerald-400"/> Pre-boarding Status</h3>
          <button onClick={()=>setActive("preboarding")} className="text-[10px] text-blue-400 hover:text-blue-300 flex items-center gap-1">Manage<ChevronRight size={10}/></button>
        </div>
        <div className="grid grid-cols-3 gap-3">
          {MOCK_CANDIDATES.map(c=>(
            <div key={c.id} className="p-3 bg-white/3 rounded-xl border border-white/8">
              <div className="flex items-center gap-2.5 mb-3">
                <Avatar initials={c.avatar} size="sm" color="emerald"/>
                <div className="min-w-0"><p className="text-xs font-semibold text-white truncate">{c.name}</p><p className="text-[10px] text-slate-500 truncate">{c.stage}</p></div>
              </div>
              <div className="h-1.5 bg-white/8 rounded-full overflow-hidden">
                <div className="h-full rounded-full" style={{width:`${c.progress}%`,background:c.progress>=90?"#34d399":c.progress>=60?"#60a5fa":"#f59e0b"}}/>
              </div>
              <div className="flex justify-between text-[10px] mt-1.5"><span className="text-slate-600">{formatDate(c.joiningDate)}</span><span className="text-slate-400 font-semibold">{c.progress}%</span></div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

// ─────────────────────────────────────────────
// SETTINGS
// ─────────────────────────────────────────────
function SettingsPage() {
  const items = [
    { label:"Notification Preferences",  desc:"Email alerts for probation reviews and interview reminders", icon:Bell,     color:"blue"   },
    { label:"Team Members & Roles",       desc:"Manage HR team access and permissions",                      icon:Users,    color:"emerald"},
    { label:"Integrations",               desc:"Connect Zoom, Teams, ATS, and calendar systems",             icon:Zap,      color:"amber"  },
    { label:"Security & Audit Log",       desc:"View access logs and configure 2FA",                         icon:Shield,   color:"purple" },
    { label:"Email Templates",            desc:"Manage invite, feedback, and rejection templates",            icon:Mail,     color:"blue"   },
    { label:"Document Templates",         desc:"Offer letter and checklist templates",                       icon:FileText, color:"emerald"},
  ];
  const clrMap = {
    blue:   "bg-blue-500/10 text-blue-400 border-blue-500/20",
    emerald:"bg-emerald-500/10 text-emerald-400 border-emerald-500/20",
    amber:  "bg-amber-500/10 text-amber-400 border-amber-500/20",
    purple: "bg-purple-500/10 text-purple-400 border-purple-500/20",
  };
  return (
    <div className="p-8 max-w-2xl">
      <div className="space-y-3">
        {items.map(({ label, desc, icon:Icon, color }) => (
          <button key={label} className="w-full flex items-center gap-4 p-4 bg-white/3 border border-white/8 rounded-2xl hover:bg-white/5 hover:border-white/12 transition-all text-left group">
            <div className={`w-10 h-10 rounded-xl ${clrMap[color]} border flex items-center justify-center flex-shrink-0`}><Icon size={16}/></div>
            <div className="flex-1 min-w-0"><p className="text-sm font-semibold text-white">{label}</p><p className="text-xs text-slate-500 mt-0.5">{desc}</p></div>
            <ChevronRight size={15} className="text-slate-600 group-hover:text-slate-400 flex-shrink-0"/>
          </button>
        ))}
      </div>
    </div>
  );
}

// ─────────────────────────────────────────────
// ROOT APP
// ─────────────────────────────────────────────
const PAGE_META = {
  dashboard:   { title:"Overview Dashboard",       subtitle:"HR operations at a glance" },
  interviews:  { title:"Interview Scheduler",      subtitle:"Manage and track all candidate interviews" },
  preboarding: { title:"Post-Offer Engagement",    subtitle:"Pre-boarding pipeline and document tracking" },
  probation:   { title:"Probation Tracker",        subtitle:"Monitor and review employee probation periods" },
  reports:     { title:"Reports & Export",         subtitle:"Download CSV reports for any module" },
  settings:    { title:"Settings",                 subtitle:"Configure your HR Suite preferences" },
};

export default function App() {
  const [currentUser, setCurrentUser] = useState(null);
  const [active, setActive]           = useState("dashboard");

  // Shared state lifted so Reports can access live data
  const [interviews]  = useState(INITIAL_INTERVIEWS);
  const [employees]   = useState(MOCK_EMPLOYEES);
  const [candidates]  = useState(MOCK_CANDIDATES);

  const STYLES = `
    * { scrollbar-width:thin; scrollbar-color:rgba(255,255,255,0.1) transparent; }
    ::-webkit-scrollbar { width:4px; height:4px; }
    ::-webkit-scrollbar-track { background:transparent; }
    ::-webkit-scrollbar-thumb { background:rgba(255,255,255,0.1); border-radius:99px; }
    input[type="date"]::-webkit-calendar-picker-indicator,
    input[type="time"]::-webkit-calendar-picker-indicator { filter:invert(0.5); }
    select option { background:#0f1623; }
    @keyframes spin { to { transform:rotate(360deg); } }
    .animate-spin { animation:spin 1s linear infinite; }
  `;

  if (!currentUser) {
    return (
      <>
        <style>{STYLES}</style>
        <LoginScreen onLogin={setCurrentUser}/>
      </>
    );
  }

  const { title, subtitle } = PAGE_META[active];

  return (
    <div className="flex h-screen bg-[#090f1c] text-white overflow-hidden" style={{ fontFamily:"'DM Sans','Inter',system-ui,sans-serif" }}>
      <style>{STYLES}</style>
      <Sidebar active={active} setActive={setActive} currentUser={currentUser} onLogout={() => setCurrentUser(null)}/>
      <main className="flex-1 flex flex-col overflow-hidden">
        <Topbar title={title} subtitle={subtitle}/>
        <div className="flex-1 overflow-y-auto">
          {active==="dashboard"   && <Overview setActive={setActive}/>}
          {active==="interviews"  && <InterviewScheduler/>}
          {active==="preboarding" && <PreboardingModule/>}
          {active==="probation"   && <ProbationTracker/>}
          {active==="reports"     && <ReportsModule interviews={interviews} employees={employees} candidates={candidates}/>}
          {active==="settings"    && <SettingsPage/>}
        </div>
      </main>
    </div>
  );
}
