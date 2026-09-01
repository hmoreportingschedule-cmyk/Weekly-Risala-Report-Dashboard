/*******************************************************
 * WEEKLY RISALA REPORT - OFFICE DASHBOARD
 * FINAL DATA / ROLE ACCESS FIX
 * Google Spreadsheet ID is fixed to the user's sheet.
 *******************************************************/

const SPREADSHEET_ID = '1eJs7TlbCbs4QcX6qdeZJhsuybcgzPr30JGb9BPtzvxI';
const TZ = 'Asia/Kolkata';

function getSS_() {
  return SpreadsheetApp.openById(SPREADSHEET_ID);
}

function doGet(e) {
  try {
    const p = (e && e.parameter) ? e.parameter : {};
    const action = String(p.action || '').trim();

    if (action === 'login') return employeeLogin_(p.userId, p.password);
    if (action === 'dashboard') return employeeDashboard_(p.token);

    if (p.checkStatus) return publicStatus_();

    if (p.pincode) return pincodeLookup_(p.pincode);

    return json_({ status: 'OK', message: 'System Active and Ready!' });
  } catch (err) {
    return json_({ status: 'Error', message: 'Backend Error: ' + err.message });
  }
}

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents || '{}');
    const ss = getSS_();
    const sheet = ss.getSheetByName('Responses');
    if (!sheet) throw new Error('Responses sheet nahi mili.');

    // Existing Responses layout:
    // Date/Time, Chain Type, Name, Contact Number, Nigran Level,
    // Zimmedar Level, Department, Risala Report, Pincode,
    // District Name, Division Name, State Name, Region Name, Country
    const row = [
      new Date(),
      data.chainType || '',
      data.userName || '',
      data.contactNumber || '',
      data.nigranLevel || '',
      data.zimmedarLevel || '',
      data.departmentList || data.department || '',
      data.risalaReport || '',
      data.pincode || '',
      data.district || '',
      data.division || '',
      data.state || '',
      data.region || '',
      data.country || 'India'
    ];

    sheet.getRange(sheet.getLastRow() + 1, 1, 1, row.length).setValues([row]);
    return json_({ status: 'Success' });
  } catch (err) {
    return json_({ status: 'Error', message: err.message });
  }
}

function publicStatus_() {
  const ss = getSS_();
  const sheet = ss.getSheetByName('Settings');
  if (!sheet) return json_({ status: 'ON', thumbnail: '', heading: 'Weekly Risala Report', subheading: '', theme: '', linkOffMsg: '' });

  const manualStatus = String(sheet.getRange('B1').getDisplayValue() || 'ON').trim().toUpperCase();
  const thumb = String(sheet.getRange('B2').getDisplayValue() || '');
  const heading = String(sheet.getRange('B3').getDisplayValue() || 'Weekly Risala Report');
  const subHeading = String(sheet.getRange('B4').getDisplayValue() || '');
  const themeName = String(sheet.getRange('B5').getDisplayValue() || '');
  const linkOffMsg = String(sheet.getRange('B7').getDisplayValue() || 'Aap Ne Deri Kardi Hai Link Off Ho Chuki Hai.');

  let finalStatus = manualStatus;
  if (manualStatus === 'AUTO') {
    const now = new Date();
    const day = Utilities.formatDate(now, TZ, 'EEEE');
    const hour = Number(Utilities.formatDate(now, TZ, 'H'));
    finalStatus = (day === 'Saturday' && hour >= 15 && hour < 18) ? 'OFF' : 'ON';
  }

  return json_({
    status: finalStatus,
    thumbnail: thumb,
    heading: heading,
    subheading: subHeading,
    theme: themeName,
    linkOffMsg: linkOffMsg
  });
}

function pincodeLookup_(pincode) {
  const sheet = getSS_().getSheetByName('PincodeMaster');
  if (!sheet) return json_({ district: 'Error', division: 'Error', state: 'Error', region: 'Error' });

  const pin = String(pincode || '').trim();
  const values = sheet.getDataRange().getDisplayValues();
  for (let i = 1; i < values.length; i++) {
    if (String(values[i][0] || '').trim() === pin) {
      return json_({
        district: values[i][1] || '',
        division: values[i][2] || '',
        state: values[i][3] || '',
        region: values[i][4] || ''
      });
    }
  }
  return json_({ district: 'Not Found', division: 'Not Found', state: 'Not Found', region: 'Not Found' });
}

function employeeLogin_(userId, password) {
  if (!userId || !password) return json_({ status: 'Error', message: 'User ID aur Password dono required hain.' });

  const sheet = getSS_().getSheetByName('User Id');
  if (!sheet) return json_({ status: 'Error', message: 'User Id sheet nahi mili. Sheet ka naam exactly User Id rakhein.' });

  const values = sheet.getDataRange().getDisplayValues();
  if (values.length < 2) return json_({ status: 'Error', message: 'User Id sheet mein users nahi hain.' });

  const h = headerMap_(values[0]);
  for (let i = 1; i < values.length; i++) {
    const r = values[i];
    const id = get_(r, h, 'User_ID').trim();
    const pw = get_(r, h, 'Password').trim();
    const status = get_(r, h, 'Status').trim().toLowerCase() || 'active';

    if (id.toLowerCase() !== String(userId).trim().toLowerCase()) continue;
    if (status !== 'active') return json_({ status: 'Error', message: 'Aapka account inactive hai.' });
    if (pw !== String(password)) return json_({ status: 'Error', message: 'Invalid User ID ya Password.' });

    const user = {
      userId: id,
      name: get_(r, h, 'Name'),
      state: get_(r, h, 'State'),
      region: get_(r, h, 'Region'),
      district: get_(r, h, 'District'),
      country: get_(r, h, 'Country') || 'India',
      department: get_(r, h, 'Department'),
      chain: get_(r, h, 'Chain Type') || get_(r, h, 'Chain') || '',
      role: get_(r, h, 'Role') || 'Viewer'
    };

    return json_({ status: 'Success', token: createSessionToken_(user), user: user });
  }

  return json_({ status: 'Error', message: 'Invalid User ID ya Password.' });
}

function employeeDashboard_(token) {
  if (!token) return json_({ status: 'Error', message: 'Login required.' });

  const user = verifySessionToken_(token);
  if (!user) return json_({ status: 'Error', message: 'Session expire ya invalid ho gayi hai. Dobara login karein.' });

  const sheet = getSS_().getSheetByName('Responses');
  if (!sheet) return json_({ status: 'Error', message: 'Responses sheet nahi mili.' });

  const lastRow = sheet.getLastRow();
  const lastCol = sheet.getLastColumn();
  if (lastRow < 2 || lastCol < 1) {
    return json_({ status: 'Success', rows: [], totalRows: 0, visibleRows: 0, reportTotal: 0, user: user });
  }

  const values = sheet.getRange(1, 1, lastRow, lastCol).getDisplayValues();
  const headers = values[0] || [];
  const h = headerMapNormalized_(headers);

  // Robust column detection for BOTH old and current headers.
  const c = {
    date: findCol_(h, ['timestamp','date','time','datetime','dateandtime'], 0),
    chain: findCol_(h, ['chaintype','chain','chainname'], 1),
    name: findCol_(h, ['username','name','employeename'], 2),
    contact: findCol_(h, ['contactnumber','contact','mobilenumber','mobile','phone'], 3),
    nigran: findCol_(h, ['nigranlevel','nigran'], 4),
    zimmedar: findCol_(h, ['zimmedarlevel','zimmedar'], 5),
    department: findCol_(h, ['departmentlist','department','departmentname'], 6),
    report: findCol_(h, ['risalareport','weeklyrisalareport','report','reports'], 7),
    pincode: findCol_(h, ['pincode','pincode6digits','pincodecode','pin'], 8),
    district: findCol_(h, ['district','districtname'], 9),
    division: findCol_(h, ['division','divisionname'], 10),
    state: findCol_(h, ['state','statename'], 11),
    region: findCol_(h, ['region','regionname'], 12),
    country: findCol_(h, ['country','countryname'], 13)
  };

  const rows = [];
  let sourceRows = 0;
  let reportTotal = 0;

  for (let i = 1; i < values.length; i++) {
    const r = values[i];
    if (r.join('').trim() === '') continue;
    sourceRows++;

    // Existing records in this Indian sheet have Country = India.
    // For older rows where Country was absent/blank, use India as fallback.
    const country = valAt_(r, c.country) || 'India';

    const x = {
      date: valAt_(r, c.date),
      chain: valAt_(r, c.chain),
      name: valAt_(r, c.name),
      contact: valAt_(r, c.contact),
      nigran: valAt_(r, c.nigran),
      zimmedar: valAt_(r, c.zimmedar),
      department: valAt_(r, c.department),
      report: valAt_(r, c.report),
      pincode: valAt_(r, c.pincode),
      district: valAt_(r, c.district),
      division: valAt_(r, c.division),
      state: valAt_(r, c.state),
      region: valAt_(r, c.region),
      country: country,
      level: valAt_(r, c.zimmedar) || valAt_(r, c.nigran)
    };

    if (canSee_(x, user)) {
      rows.push(x);
      const n = Number(String(x.report || '').replace(/,/g, ''));
      if (!isNaN(n)) reportTotal += n;
    }
  }

  return json_({
    status: 'Success',
    rows: rows,
    totalRows: sourceRows,
    visibleRows: rows.length,
    reportTotal: reportTotal,
    user: user,
    debug: { headers: headers, columns: c, sourceRows: sourceRows }
  });
}

/**************** ACCESS CONTROL ****************/
function canSee_(x, user) {
  const roles = String(user.role || 'Viewer')
    .split(',')
    .map(s => s.trim().toLowerCase())
    .filter(Boolean);

  if (!roles.length || roles.includes('viewer')) {
    // If Viewer has explicit scope fields, use those. Otherwise allow all.
    const hasScope = !!(user.country || user.state || user.region || user.district || user.department || user.chain);
    if (!hasScope) return true;
    return (!user.country || same_(x.country, user.country)) &&
           (!user.state || same_(x.state, user.state)) &&
           (!user.region || same_(x.region, user.region)) &&
           (!user.district || same_(x.district, user.district)) &&
           (!user.department || same_(x.department, user.department)) &&
           (!user.chain || chainAssigned_(x.chain, user.chain));
  }

  if (roles.includes('admin') || roles.includes('administrator') || roles.includes('all')) return true;

  // Multiple roles are treated as AND scope. Example: Country,Department.
  for (let i = 0; i < roles.length; i++) {
    const role = roles[i];
    if (role === 'country') {
      if (!same_(x.country || 'India', user.country || 'India')) return false;
    } else if (role === 'state') {
      if (!same_(x.state, user.state)) return false;
    } else if (role === 'region') {
      if (!same_(x.region, user.region)) return false;
    } else if (role === 'district') {
      if (!same_(x.district, user.district)) return false;
    } else if (role === 'department') {
      if (!same_(x.department, user.department)) return false;
    } else if (role === 'chain') {
      if (user.chain && !chainAssigned_(x.chain, user.chain)) return false;
    } else if (role === 'division') {
      if (!same_(x.division, user.division)) return false;
    }
  }

  // If Chain Type is assigned in User Id, it is an additional mandatory scope.
  // Leave the User Id Chain Type cell blank to allow all chains.
  if (user.chain && !chainAssigned_(x.chain, user.chain)) return false;

  return true;
}

/**************** SESSION ****************/
function base64UrlEncode_(text) {
  return Utilities.base64EncodeWebSafe(Utilities.newBlob(String(text)).getBytes()).replace(/=+$/, '');
}
function base64UrlDecode_(text) {
  let s = String(text).replace(/-/g, '+').replace(/_/g, '/');
  while (s.length % 4) s += '=';
  return Utilities.newBlob(Utilities.base64Decode(s)).getDataAsString();
}
function getSessionSecret_() {
  const props = PropertiesService.getScriptProperties();
  let secret = props.getProperty('RISALA_SESSION_SECRET');
  if (!secret) {
    secret = Utilities.getUuid() + Utilities.getUuid() + Utilities.getUuid();
    props.setProperty('RISALA_SESSION_SECRET', secret);
  }
  return secret;
}
function signSession_(payload) {
  const bytes = Utilities.computeHmacSha256Signature(payload, getSessionSecret_());
  return Utilities.base64EncodeWebSafe(bytes).replace(/=+$/, '');
}
function createSessionToken_(user) {
  const payload = JSON.stringify({ u: user, exp: Date.now() + 24 * 60 * 60 * 1000, v: 2 });
  const encoded = base64UrlEncode_(payload);
  return encoded + '.' + signSession_(encoded);
}
function verifySessionToken_(token) {
  try {
    const parts = String(token || '').split('.');
    if (parts.length !== 2) return null;
    if (signSession_(parts[0]) !== parts[1]) return null;
    const data = JSON.parse(base64UrlDecode_(parts[0]));
    if (!data || !data.exp || Date.now() > Number(data.exp) || !data.u) return null;
    return data.u;
  } catch (err) {
    return null;
  }
}

/**************** HELPERS ****************/
function normalizeHeader_(s) {
  return String(s == null ? '' : s)
    .toLowerCase()
    .replace(/[\s_\-()./:]+/g, '')
    .replace(/[^a-z0-9]/g, '');
}
function headerMapNormalized_(row) {
  const m = {};
  for (let i = 0; i < row.length; i++) m[normalizeHeader_(row[i])] = i;
  return m;
}
function headerMap_(row) {
  const m = {};
  for (let i = 0; i < row.length; i++) m[String(row[i]).trim()] = i;
  return m;
}
function findCol_(h, names, fallback) {
  for (let i = 0; i < names.length; i++) {
    const k = normalizeHeader_(names[i]);
    if (h[k] !== undefined) return h[k];
  }
  return fallback;
}
function get_(r, h, n) {
  return h[n] !== undefined ? String(r[h[n]] || '') : '';
}
function valAt_(r, i) {
  return (i >= 0 && i < r.length) ? String(r[i] == null ? '' : r[i]).trim() : '';
}
function chainAssigned_(rowChain, assignedChain) {
  const allowed = String(assignedChain || '')
    .split(',')
    .map(s => s.trim())
    .filter(Boolean);
  if (!allowed.length) return true;
  return allowed.some(v => same_(rowChain, v));
}

function same_(a, b) {
  return String(a || '').trim().toLowerCase() === String(b || '').trim().toLowerCase();
}
function json_(obj) {
  return ContentService.createTextOutput(JSON.stringify(obj)).setMimeType(ContentService.MimeType.JSON);
}
