# Counties-Keyboard-Navigation
package main

import(
	"fmt"
	"bufio"
	"os"
	"strconv"
	"math")

func nextcounty (county_info map[string]vertex) {

	current_county := "Eaton"
	var county_loc vertex
	var distance_near float64
	var distance_hold float64
	var new_county string

	fmt.Println("Enter the direction you wish to move: W(north), E(northeast), D(East), C(southeast), X(south), Z(southwest), A(west), Q(northwest)")
	reader := bufio.NewScanner(os.Stdin)
	var request string
	fmt.Println("You are in:", current_county)
	for reader.Scan(){
		request = reader.Text()
		//fmt.Println(request)
    
		distance_hold = 1000000
		distance_near = 1000000
		county_loc = county_info[current_county]
		new_county = "error"
		switch request{
		case "w":
			fmt.Println("You moved North.")
			for county, info := range county_info {
				if (info.lat > county_loc.lat) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		case "e":
			fmt.Println("You moved NorthEast.")
			for county, info := range county_info {
				if (info.lat > county_loc.lat && info.long > county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}

		case "d":
			fmt.Println("You moved East.")
			for county, info := range county_info {
				if (info.long > county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}

		case "c":
			fmt.Println("You moved SouthEast.")
			for county, info := range county_info {
				if (info.lat < county_loc.lat && info.long > county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		case "x":
			fmt.Println("You moved South.")
			for county, info := range county_info {
				if (info.lat < county_loc.lat) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		case "z": 
			fmt.Println("You moved SouthWest.")
			for county, info := range county_info {
				if (info.lat < county_loc.lat && info.long < county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		case "a":
			fmt.Println("You moved West.")
			for county, info := range county_info {
				if (info.long < county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		case "q":
			fmt.Println("You Moved NorthWest.")
			for county, info := range county_info {
				if (info.lat > county_loc.lat && info.long < county_loc.long) {
					distance_hold = distance(county_loc, info) 
						if (distance_hold < distance_near) {
							distance_near = distance_hold
							new_county = county
						}
				}
			}
		default:
			fmt.Println("This is not a valid driection key, please try again.")
		}

		current_county = new_county
		fmt.Println("You are in", current_county)
	}
}


func haversine (theta float64) float64 {
	return math.Pow(math.Sin(theta/2), 2)
}
func distance(county1 vertex, county2 vertex) float64 {
	var lat1, long1, lat2, long2, r float64

	lat1 = county1.lat * math.Pi / 180
	long1 = county1.long * math.Pi / 180
	lat2 = county2.lat * math.Pi / 180
	long2 = county2.long * math.Pi / 180

	r = 3959 // radius of earth in miles

	h := haversine(lat2-lat1) + math.Cos(lat1)*math.Cos(lat2)*haversine(long2-long1)

	return 2 * r * math.Asin(math.Sqrt(h))
}

type vertex struct {
	lat float64
	long float64
}
func check(e error) {
    if e != nil {
        panic(e)
    }
}

func main(){
	var county_info map[string]vertex
	var coords vertex
	var name string
	//var current_name string
	//var current_vertex vertex
	
	county_info = make(map[string]vertex)

	file, err := os.Open("latlong.txt")
	check(err)

	defer file.Close()

	scan := bufio.NewScanner(file)
	scan.Split(bufio.ScanWords)

	var lat_d string
	var long_d string

	i := 1

	for scan.Scan() {
		if i % 3 == 1 {
			name = scan.Text()
		}
		if i % 3 == 2 {
			lat_d = scan.Text()
		}
		if i % 3 == 0 {
			long_d = scan.Text()
			coords.lat, _ = (strconv.ParseFloat(lat_d, 64))
			coords.long, _ = (strconv.ParseFloat(long_d, 64))
			county_info[name] = coords
		}
		i = i+1
	}

	for k, v := range county_info {
		fmt.Println(k, v)
	}
	nextcounty(county_info)
}

