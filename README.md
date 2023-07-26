# swift_unsplash_api

```swift
import Foundation

struct UnsplashPhoto: Codable, Identifiable {
    let id: String
    let urls: [String: URL]
}

protocol UnsplashServiceProtocol {
    init(accessKey: String)
    func getRandomPhoto() async throws -> UnsplashPhoto
}

final class UnsplashService: UnsplashServiceProtocol {
    
    private let accessKey: String
    private var token: String {
        "Client-ID \(self.accessKey)"
    }
    
    init(accessKey: String) {
        self.accessKey = accessKey
    }
    
    func getRandomPhoto() async throws -> UnsplashPhoto {
        guard let url = URL(string: "https://api.unsplash.com/photos/random") else {
            throw GenericError(message: "Invalid URL")
        }
        var request = URLRequest(url: url)
        request.setValue(self.token, forHTTPHeaderField: "Authorization")
        let (data, _): (Data, URLResponse) = try await URLSession.shared.data(for: request)
        let json = try JSONSerialization.jsonObject(with: data)
        return try JSONDecoder().decode(UnsplashPhoto.self, from: data)
    }
    
}

```
