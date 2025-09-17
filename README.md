# AI-PROJECT
Text to image approach 

"use client";

import { useState, useRef } from "react";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { Download, Sparkles, Image as ImageIcon } from "lucide-react";

export default function TextToImageGenerator() {
  const [prompt, setPrompt] = useState("");
  const [style, setStyle] = useState("realistic");
  const [generatedImage, setGeneratedImage] = useState<string | null>(null);
  const [isGenerating, setIsGenerating] = useState(false);
  const [progress, setProgress] = useState(0);
  const intervalRef = useRef<NodeJS.Timeout | null>(null);

  const handleGenerate = () => {
    if (!prompt.trim()) return;
    
    setIsGenerating(true);
    setProgress(0);
    setGeneratedImage(null);
    
    // Simulate progress
    intervalRef.current = setInterval(() => {
      setProgress(prev => {
        if (prev >= 100) {
          if (intervalRef.current) clearInterval(intervalRef.current);
          return 100;
        }
        return prev + 10;
      });
    }, 200);
    
    // Simulate image generation
    setTimeout(() => {
      if (intervalRef.current) clearInterval(intervalRef.current);
      setIsGenerating(false);
      // In a real app, this would be the actual generated image URL
      setGeneratedImage("/api/placeholder/512/512");
    }, 2200);
  };

  const handleDownload = () => {
    if (!generatedImage) return;
    
    const link = document.createElement("a");
    link.href = generatedImage;
    link.download = `generated-${prompt.substring(0, 20)}.png`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };

  const handleReset = () => {
    setPrompt("");
    setGeneratedImage(null);
    setProgress(0);
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
      intervalRef.current = null;
    }
    setIsGenerating(false);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-indigo-50 to-purple-100 py-12 px-4 sm:px-6">
      <div className="max-w-4xl mx-auto">
        <Card className="shadow-xl">
          <CardHeader className="text-center">
            <CardTitle className="text-3xl font-bold text-gray-800 flex items-center justify-center">
              <Sparkles className="mr-2 h-8 w-8 text-indigo-600" />
              AI Text-to-Image Generator
            </CardTitle>
            <CardDescription className="text-gray-600">
              Transform your ideas into stunning visuals with AI
            </CardDescription>
          </CardHeader>
          <CardContent className="space-y-8">
            {/* Prompt Input */}
            <div className="space-y-4">
              <Label htmlFor="prompt" className="text-lg font-medium text-gray-700">
                Describe your image
              </Label>
              <Textarea
                id="prompt"
                placeholder="A futuristic cityscape at sunset with flying cars and neon lights..."
                value={prompt}
                onChange={(e) => setPrompt(e.target.value)}
                className="min-h-[120px] text-lg"
                disabled={isGenerating}
              />
              
              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div className="space-y-2">
                  <Label htmlFor="style" className="text-gray-700">
                    Art Style
                  </Label>
                  <Select value={style} onValueChange={setStyle} disabled={isGenerating}>
                    <SelectTrigger id="style">
                      <SelectValue placeholder="Select style" />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="realistic">Realistic</SelectItem>
                      <SelectItem value="digital-art">Digital Art</SelectItem>
                      <SelectItem value="oil-painting">Oil Painting</SelectItem>
                      <SelectItem value="watercolor">Watercolor</SelectItem>
                      <SelectItem value="anime">Anime</SelectItem>
                      <SelectItem value="pixel-art">Pixel Art</SelectItem>
                      <SelectItem value="cyberpunk">Cyberpunk</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
                
                <div className="flex items-end">
                  <Button 
                    onClick={handleGenerate} 
                    disabled={isGenerating || !prompt.trim()}
                    className="w-full py-6 text-lg"
                  >
                    {isGenerating ? (
                      <span className="flex items-center">
                        <span className="animate-spin rounded-full h-4 w-4 border-b-2 border-white mr-2"></span>
                        Generating...
                      </span>
                    ) : (
                      <span className="flex items-center">
                        <Sparkles className="mr-2 h-5 w-5" />
                        Generate Image
                      </span>
                    )}
                  </Button>
                </div>
              </div>
            </div>

            {/* Progress Bar */}
            {isGenerating && (
              <div className="space-y-3">
                <div className="flex justify-between text-sm text-gray-600">
                  <span>Creating your masterpiece...</span>
                  <span>{progress}%</span>
                </div>
                <div className="h-3 bg-gray-200 rounded-full overflow-hidden">
                  <div 
                    className="h-full bg-indigo-600 transition-all duration-300 ease-out"
                    style={{ width: `${progress}%` }}
                  ></div>
                </div>
              </div>
            )}

            {/* Generated Image Display */}
            {generatedImage && (
              <div className="space-y-6">
                <div className="flex flex-col items-center">
                  <h3 className="text-xl font-semibold text-gray-800 mb-4 flex items-center">
                    <ImageIcon className="mr-2 h-5 w-5" />
                    Generated Image
                  </h3>
                  <div className="relative border-4 border-white rounded-xl shadow-lg overflow-hidden">
                    <img 
                      src={generatedImage} 
                      alt="Generated from prompt" 
                      className="w-full max-w-2xl object-contain"
                    />
                    <div className="absolute inset-0 bg-gradient-to-t from-black/70 to-transparent opacity-0 hover:opacity-100 transition-opacity flex items-end p-6">
                      <p className="text-white text-sm font-medium">{prompt}</p>
                    </div>
                  </div>
                </div>
                
                <div className="flex flex-col sm:flex-row justify-center gap-4">
                  <Button onClick={handleDownload} className="flex items-center">
                    <Download className="mr-2 h-4 w-4" />
                    Download Image
                  </Button>
                  <Button variant="outline" onClick={handleReset}>
                    Create New
                  </Button>
                </div>
              </div>
            )}

            {/* Examples Section */}
            {!generatedImage && !isGenerating && (
              <div className="bg-indigo-50 rounded-xl p-6 border border-indigo-100">
                <h3 className="font-semibold text-indigo-800 mb-3">Try these examples:</h3>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-3">
                  {[
                    "A serene mountain landscape at sunrise with misty valleys",
                    "A cyberpunk marketplace with neon signs and futuristic vendors",
                    "An astronaut cat floating in space with distant planets"
                  ].map((example, idx) => (
                    <button
                      key={idx}
                      onClick={() => setPrompt(example)}
                      className="text-left p-3 bg-white rounded-lg border border-indigo-200 text-sm hover:bg-indigo-100 transition-colors"
                    >
                      {example}
                    </button>
                  ))}
                </div>
              </div>
            )}

            {/* Info Section */}
            <div className="bg-gradient-to-r from-indigo-500 to-purple-600 rounded-xl p-6 text-white">
              <h3 className="font-bold text-lg mb-2">How It Works</h3>
              <ul className="list-disc pl-5 space-y-1 text-indigo-100">
                <li>Describe your vision in the text box</li>
                <li>Select your preferred art style</li>
                <li>Click "Generate Image" to create your artwork</li>
                <li>Download and share your creations</li>
              </ul>
            </div>
          </CardContent>
        </Card>
        
        <div className="mt-8 text-center text-sm text-gray-500">
          <p>Powered by state-of-the-art AI image generation technology</p>
        </div>
      </div>
    </div>
  );
}
