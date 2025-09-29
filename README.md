# College Timetabling Tool

A comprehensive, modular web-based timetable management system for educational institutions. Built with vanilla JavaScript using ES6 modules for maximum reusability and maintainability.

## Features

- **Schedule Management**: Add, edit, and delete class schedules
- **Conflict Detection**: Automatic detection of trainer and venue conflicts
- **Multi-Department Support**: 7 departments with 60+ trainers
- **Venue Management**: 45 venues with capacity tracking
- **Filtering & Search**: Advanced filtering by course, trainer, venue, day, and time
- **Export Options**: CSV, JSON, and text reports
- **Responsive Design**: Works on desktop, tablet, and mobile devices
- **No Database Required**: Runs entirely in the browser
- **Modular Architecture**: Easily reusable components

## Project Structure

```
college-timetable/
├── index.html                      # Main HTML file
├── css/
│   └── styles.css                  # All styling
├── js/
│   ├── config/
│   │   ├── courses.js             # Course and time slot configurations
│   │   ├── venues.js              # Venue data with capacities
│   │   └── trainers.js            # Trainer data organized by department
│   ├── models/
│   │   └── TimetableData.js       # Data management and CRUD operations
│   ├── services/
│   │   ├── ConflictChecker.js     # Conflict detection and validation
│   │   ├── ExportService.js       # Export to CSV, JSON, text reports
│   │   └── FilterService.js       # Filtering and search operations
│   └── app.js                      # Main application initialization
└── README.md                       # This file
```

## Installation

### Option 1: Direct Use
1. Download all files maintaining the directory structure
2. Open `index.html` in a modern web browser
3. No build process or server required!

### Option 2: GitHub Pages Deployment
1. Fork or clone this repository
2. Go to repository Settings → Pages
3. Select the main branch as source
4. Access at `https://yourusername.github.io/repository-name/`

### Option 3: Local Development Server
```bash
# Using Python 3
python -m http.server 8000

# Using Node.js http-server
npx http-server

# Then open http://localhost:8000
```

## Usage

### Basic Operations

#### Adding a Class
1. Click "Add New Class" button
2. Select course, day, and time slot
3. Enter intake, subject, trainer, and venue
4. Click "Save Class"

#### Editing a Class
1. Click on any scheduled class in the timetable
2. Modify the information
3. Click "Save Class"

#### Filtering
- Use the filter dropdowns to view specific courses, trainers, or venues
- Filters work together (AND logic)
- Clear filters by selecting "All"

#### Exporting
- Click "Export Schedule"
- Choose format: CSV, JSON, or REPORT
- File downloads automatically

### Code Examples

#### Using Individual Modules

```javascript
// Import only what you need
import { venues, getVenueByName } from './js/config/venues.js';
import ConflictChecker from './js/services/ConflictChecker.js';
import TimetableData from './js/models/TimetableData.js';

// Create data store
const timetable = new TimetableData();

// Add a class
timetable.addClass({
    day: 'Monday',
    courseCode: 'DME',
    intake: 'SEP 25',
    timeSlot: '0',
    subject: 'Engineering Maths',
    trainer: 'Dorothy Adhiambo',
    venue: 'ASC 2'
});

// Check for conflicts
const checker = new ConflictChecker(timetable);
const conflicts = checker.checkConflicts('Monday', '0', 'Dorothy Adhiambo', 'ASC 2');

if (conflicts.length === 0) {
    console.log('No conflicts!');
}
```

#### Finding Available Venues

```javascript
import { venues, getVenuesByCapacityRange } from './js/config/venues.js';

// Find large venues (capacity > 100)
const largeVenues = getVenuesByCapacityRange(100, 200);
console.log(largeVenues);

// Get specific venue info
const venue = getVenueByName('LH 2');
console.log(venue.capacity); // 50
```

#### Exporting Custom Reports

```javascript
import ExportService from './js/services/ExportService.js';

const exportService = new ExportService(timetableData);

// Export trainer's schedule
const trainerCSV = exportService.exportByTrainer('Dorothy Adhiambo');

// Export course schedule
const courseCSV = exportService.exportByCourse('DME');

// Generate weekly report
const report = exportService.generateWeeklyReport();
```

#### Advanced Filtering

```javascript
import FilterService from './js/services/FilterService.js';

const filterService = new FilterService(timetableData);

// Search by subject
const mathClasses = filterService.searchBySubject('mathematics');

// Advanced search
const results = filterService.advancedSearch({
    subject: 'engineering',
    trainer: 'Dorothy',
    day: 'Monday'
});

// Get filter options for dropdowns
const options = filterService.getFilterOptions();
console.log(options.trainers); // All unique trainers
```

## Module Documentation

### Configuration Modules

#### courses.js
Defines available courses, time slots, and days.

**Key Exports:**
- `courses` - Array of course objects
- `timeSlots` - Array of time slot strings
- `days` - Array of weekday names
- `getCourseByCode(code)` - Get course details
- `getDefaultIntakes(courseCode)` - Get intake groups

#### venues.js
Contains all venue information with capacities.

**Key Exports:**
- `venues` - Array of venue objects with capacities
- `getVenueByName(name)` - Get venue details
- `getVenueCapacity(name)` - Get capacity
- `getVenuesByCapacityRange(min, max)` - Filter by size

#### trainers.js
Organizes trainers by department.

**Key Exports:**
- `trainersByDepartment` - Object mapping departments to trainers
- `getAllTrainers()` - Get flat array of all trainers
- `getTrainersByDepartment(dept)` - Get trainers in a department
- `findTrainerDepartment(name)` - Find trainer's department
- `searchTrainers(term)` - Search trainers by name

### Data Model

#### TimetableData.js
Central data store with CRUD operations.

**Key Methods:**
- `addClass(classData)` - Add or update a class
- `getClass(key)` - Get class by key
- `removeClass(key)` - Delete a class
- `getAllClasses()` - Get all classes
- `getClassesByCourse(code)` - Filter by course
- `getClassesByTrainer(name)` - Filter by trainer
- `getClassesByVenue(name)` - Filter by venue
- `getStatistics()` - Get usage statistics
- `exportToJSON()` / `importFromJSON()` - Data persistence

### Service Modules

#### ConflictChecker.js
Validates schedules and detects conflicts.

**Key Methods:**
- `checkConflicts(day, time, trainer, venue, excludeKey)` - Check for conflicts
- `isTrainerAvailable(trainer, day, time)` - Check availability
- `isVenueAvailable(venue, day, time)` - Check availability
- `getTrainerSchedule(trainer)` - Get all classes for trainer
- `getVenueSchedule(venue)` - Get all classes for venue
- `validate(classData)` - Validate before adding

#### ExportService.js
Handles data export in multiple formats.

**Key Methods:**
- `toCSV()` - Export as CSV string
- `downloadCSV(filename)` - Download CSV file
- `toJSON()` - Export as JSON string
- `downloadJSON(filename)` - Download JSON file
- `exportByTrainer(name)` - Export trainer's schedule
- `exportByCourse(code)` - Export course schedule
- `generateWeeklyReport()` - Create text report

#### FilterService.js
Implements filtering and search functionality.

**Key Methods:**
- `setFilter(type, value)` - Set a filter
- `clearAllFilters()` - Clear all filters
- `getFilteredClasses()` - Get filtered results
- `searchBySubject(term)` - Search by subject name
- `advancedSearch(criteria)` - Multi-criteria search
- `getFilterOptions()` - Get unique values for filters

## Data Structure

### Class Object Format

```javascript
{
    day: 'Monday',              // Day of the week
    courseCode: 'DME',          // Course code
    intake: 'SEP 25',           // Intake group
    timeSlot: '0',              // Time slot index (0-2)
    subject: 'Engineering Math',// Subject/unit name
    trainer: 'Dorothy Adhiambo',// Trainer name
    venue: 'ASC 2',             // Venue name
    key: 'Monday-DME-SEP25-0'  // Auto-generated unique key
}
```

## Customization

### Adding New Courses
Edit `js/config/courses.js`:

```javascript
export const courses = [
    { code: 'DME', name: 'Diploma Medical Engineering' },
    { code: 'NEW', name: 'Your New Course' },  // Add here
    // ...
];
```

### Adding New Venues
Edit `js/config/venues.js`:

```javascript
export const venues = [
    { name: 'Workshop', capacity: 55 },
    { name: 'New Lab', capacity: 40 },  // Add here
    // ...
];
```

### Adding New Trainers
Edit `js/config/trainers.js`:

```javascript
export const trainersByDepartment = {
    "Your Department": [
        "New Trainer Name",
        // ...
    ],
    // ...
};
```

## Browser Compatibility

- Chrome 61+ ✓
- Firefox 60+ ✓
- Safari 11+ ✓
- Edge 79+ ✓

Requires ES6 module support. Not compatible with Internet Explorer.

## Limitations

- **No Persistence**: Data is stored in memory only. Refresh clears data.
- **Client-Side Only**: No backend or database integration.
- **Single User**: No multi-user collaboration features.

## Future Enhancements

Potential additions for future versions:
- LocalStorage persistence
- Backend API integration
- Print-friendly timetable views
- Drag-and-drop scheduling
- Calendar view (monthly/weekly)
- Email notifications
- Student enrollment tracking
- Resource utilization reports
- Mobile app version

## Contributing

This is a modular, open architecture. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes in the appropriate module
4. Test thoroughly
5. Submit a pull request

## License

MIT License - Feel free to use in your institution.

## Support

For issues or questions:
- Open an issue on GitHub
- Check existing documentation
- Review code comments in modules

## Credits

Developed for IMPERIAL COLLEGE OF MEDICAL AND HEALTH SCIENCES
BY: ANDREW MUNGAI 
Initial deployment: 2025

---

**Note**: This tool stores data in browser memory. For production use with data persistence, consider adding a backend service or browser storage implementation.