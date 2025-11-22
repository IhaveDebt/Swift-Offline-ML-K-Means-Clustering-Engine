import Foundation

struct Point {
    var x: Double
    var y: Double
}

class KMeans {
    var k: Int
    var points: [Point]
    var centroids: [Point] = []
    
    init(k: Int, points: [Point]) {
        self.k = k
        self.points = points
        self.centroids = (0..<k).map { _ in
            points.randomElement()!
        }
    }
    
    func distance(_ a: Point, _ b: Point) -> Double {
        let dx = a.x - b.x
        let dy = a.y - b.y
        return sqrt(dx*dx + dy*dy)
    }
    
    func train(iterations: Int) {
        for _ in 0..<iterations {
            var clusters: [[Point]] = Array(repeating: [], count: k)
            
            for p in points {
                let idx = centroids.enumerated().min(by: {
                    distance(p, $0.element) < distance(p, $1.element)
                })!.0
                clusters[idx].append(p)
            }
            
            for i in 0..<k {
                let cluster = clusters[i]
                guard !cluster.isEmpty else { continue }
                let avgX = cluster.map { $0.x }.reduce(0, +) / Double(cluster.count)
                let avgY = cluster.map { $0.y }.reduce(0, +) / Double(cluster.count)
                centroids[i] = Point(x: avgX, y: avgY)
            }
        }
    }
    
    func printCentroids() {
        print("FINAL CENTROIDS:")
        centroids.enumerated().forEach {
            print("Cluster \($0): (\($1.x), \($1.y))")
        }
    }
}

// Demo
let points = [
    Point(x: 1, y: 1), Point(x: 2, y: 1),
    Point(x: 8, y: 9), Point(x: 9, y: 8),
    Point(x: 50, y: 50), Point(x: 52, y: 49)
]

let model = KMeans(k: 3, points: points)
model.train(iterations: 10)
model.printCentroids()
