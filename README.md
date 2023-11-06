interface Band {
    id: number;
    name: string;
    formed: string;
    location: string;
    country: string;
    members: string[];
    albums: Album[];
}

interface Album {
    title: string;
    year: number;
    genre: string;
}

type BandSorter = (first: Band, second: Band) => number;

document.addEventListener("DOMContentLoaded", siteCode)

let bands: Band[] = [];

async function siteCode() {
    const data = await loadData();
    bands = data;

    fillCountries(bands);

    displayBands(bands);

    const nameSort = document.getElementById("sort-name")!;
    nameSort.addEventListener("click", sortByName);

    const idSort = document.getElementById("sort-id")!;
    idSort.addEventListener("click", sortById);

    const applyFilterButton = document.getElementById("apply-filter")!;
    applyFilterButton.addEventListener("click", applyFilter)
}

const nameSorter: BandSorter = (first, second) => first.name.localeCompare(second.name);
const idSorter: BandSorter = (first, second) => first.id - second.id;

const sortByName = () => {
    const sortedBands = bands.sort(nameSorter);
    displayBands(sortedBands);
}

const sortById = () => {
    const sortedBands = bands.sort(idSorter);
    displayBands(sortedBands);
}

const fillCountries = (bands: Band[]) => {
    const filter = document.getElementById("country-filter")!;

    const countries = new Set<string>();
    for (const band of bands) {
        countries.add(band.country);
    }

    for (const country of countries) {
        const option = document.createElement("option");
        option.value = country;
        option.innerHTML = country;
        filter.appendChild(option);
    }
}

const applyFilter = () => {
    const bandNameElement = document.getElementById("band-name-filter") as HTMLInputElement;
    const bandName = bandNameElement.value.toLowerCase();

    const countryElement = document.getElementById("country-filter") as HTMLSelectElement;
    const country = countryElement.value;

    const formedYearElement = document.getElementById("formed-year-filter") as HTMLInputElement;
    const formedYear = parseInt(formedYearElement.value);

    let filteredBands = bands;

    if (bandName) {
        filteredBands = filteredBands.filter(band => band.name.toLowerCase().includes(bandName));
    }

    if (country !== "all") {
        filteredBands = filteredBands.filter(band => band.country === country);
    }

    if (!isNaN(formedYear)) {
        filteredBands = filteredBands.filter(band => new Date(band.formed).getFullYear() === formedYear);
    }

    displayBands(filteredBands);
}

const loadData = async () => {
    const dataUri = "https://raw.githubusercontent.com/sweko/uacs-internet-programming-exams/main/dry-run-mid-term/data/bands.json";
    const response = await fetch(dataUri);

    if (!response.ok) {
        throw new Error("Failed to fetch data");
    }

    const data = await response.json();
    return data;
}

const displayBands = (bands: Band[]) => {
    const container = document.getElementById("band-container")!;
    container.innerHTML = "";

    for (const band of bands) {
        const bandRow = generateBandRow(band);
        container.appendChild(bandRow);
    }
}

const generateBandRow = (band: Band) => {
    const row = document.createElement("div");
    row.classList.add("band-row");

    const idCell = document.createElement("div");
    idCell.classList.add("band-data", "band-id");
    idCell.innerHTML = band.id.toString();
    row.appendChild(idCell);

    const nameCell = document.createElement("div");
    nameCell.classList.add("band-data", "band-name");
    nameCell.innerHTML = band.name;
    row.appendChild(nameCell);

    const formedCell = document.createElement("div");
    formedCell.classList.add("band-data", "band-formed");
    formedCell.innerHTML = band.formed;
    row.appendChild(formedCell);

    const locationCell = document.createElement("div");
    locationCell.classList.add("band-data", "band-location");
    locationCell.innerHTML = band.location;
    row.appendChild(locationCell);

    const countryCell = document.createElement("div");
    countryCell.classList.add("band-data", "band-country");
    countryCell.innerHTML = band.country;
    row.appendChild(countryCell);

    const membersCell = document.createElement("div");
    membersCell.classList.add("band-data", "band-members");
    membersCell.innerHTML = band.members.join(", ");
    row.appendChild(membersCell);

    const albumsCell = document.createElement("div");
    albumsCell.classList.add("band-data", "band-albums");

    for (const album of band.albums) {
        const albumInfo = document.createElement("div");
        albumInfo.classList.add("album-info");
        albumInfo.innerHTML = `${album.title} (${album.year}) - Genre: ${album.genre}`;
        albumsCell.appendChild(albumInfo);
    }

    row.appendChild(albumsCell);

    return row;
}
