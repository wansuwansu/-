import React, { useState } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Card, CardContent } from "@/components/ui/card";

export default function TravelLog() {
  const [regions, setRegions] = useState([
    {
      name: "서울",
      subregions: [],
      newSubregion: "",
    },
  ]);

  const [newRegion, setNewRegion] = useState("");

  const addRegion = () => {
    if (!newRegion.trim()) return;
    setRegions([
      ...regions,
      { name: newRegion.trim(), subregions: [], newSubregion: "" },
    ]);
    setNewRegion("");
  };

  const updateSubregionInput = (regionIndex, value) => {
    const updated = [...regions];
    updated[regionIndex].newSubregion = value;
    setRegions(updated);
  };

  const addSubregion = (regionIndex) => {
    const updated = [...regions];
    const newSub = updated[regionIndex].newSubregion.trim();
    if (newSub) {
      updated[regionIndex].subregions.push({ name: newSub, text: "", image: null });
      updated[regionIndex].newSubregion = "";
      setRegions(updated);
    }
  };

  return (
    <div className="p-4 space-y-4">
      <h1 className="text-2xl font-bold">여행 기록</h1>

      <div className="flex gap-2">
        <Input
          placeholder="메인 지역 추가 (예: 부산)"
          value={newRegion}
          onChange={(e) => setNewRegion(e.target.value)}
        />
        <Button onClick={addRegion}>+</Button>
      </div>

      {regions.map((region, regionIndex) => (
        <Card key={regionIndex} className="bg-gray-100">
          <CardContent className="p-4">
            <h2 className="text-xl font-semibold">{region.name}</h2>
            <div className="flex gap-2 mt-2">
              <Input
                placeholder="세부 지역 추가 (예: 홍대)"
                value={region.newSubregion}
                onChange={(e) => updateSubregionInput(regionIndex, e.target.value)}
              />
              <Button onClick={() => addSubregion(regionIndex)}>+</Button>
            </div>

            <div className="grid grid-cols-2 gap-4 mt-4">
              {region.subregions.map((sub, subIndex) => (
                <Card key={subIndex} className="bg-white">
                  <CardContent className="p-3">
                    <h3 className="font-medium">{sub.name}</h3>
                    {/* 추후에 텍스트 및 이미지 추가 기능을 여기에 구현할 수 있습니다 */}
                  </CardContent>
                </Card>
              ))}
            </div>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}
